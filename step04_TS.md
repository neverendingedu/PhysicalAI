제공해주신 코드를 보면, 현재 실행 중인 시뮬레이션 환경의 뼈대가 되는 **`VialsToRackEnvCfg`** 및 **`VialsToRackDREnvCfg`** 클래스에 에피소드 길이(녹화 시간, `episode_length_s`)가 설정되어 있지 않습니다.

이 때문에 부모 클래스에 숨겨진 매우 긴 기본 설정값을 그대로 상속받게 되고, 파이토치(PyTorch)가 한 번에 3GB 이상의 거대한 메모리를 미리 잡으려다 VRAM(8GB) 한도를 초과해버린 것입니다.

아래 안내에 따라 코드의 **밑에서 25번째 줄 부근**에 있는 **`VialsToRackEnvCfg`** 클래스에 최대 시간을 제한하는 코드를 3줄 추가해 주시면 됩니다. (부모 클래스에 설정해두면 DR 클래스에도 자동으로 적용됩니다.)

---

### 🛠️ 파일 수정 방법

**[수정 전]** (기존 코드)

```python
@configclass
class VialsToRackEnvCfg(SO101TaskEnvCfg):
    """
    Base config.
    """
    scene: VialsToRackSceneCfg = VialsToRackSceneCfg()
    events: VialsToRackEventCfg = VialsToRackEventCfg()
    observations: VialsToRackObservationsCfg = VialsToRackObservationsCfg()


@configclass
class VialsToRackDREnvCfg(VialsToRackEnvCfg):

```

👇👇👇

**[수정 후]** (`def __post_init__` 함수 부분 추가)

```python
@configclass
class VialsToRackEnvCfg(SO101TaskEnvCfg):
    """
    Base config.
    """
    scene: VialsToRackSceneCfg = VialsToRackSceneCfg()
    events: VialsToRackEventCfg = VialsToRackEventCfg()
    observations: VialsToRackObservationsCfg = VialsToRackObservationsCfg()

    def __post_init__(self) -> None:
        super().__post_init__()
        self.episode_length_s = 15.0  # 메모리 초과 방지를 위해 최대 녹화 시간 15초로 제한


@configclass
class VialsToRackDREnvCfg(VialsToRackEnvCfg):

```

> ⚠️ **주의사항:** 파이썬은 **들여쓰기(띄어쓰기 4칸)**가 매우 중요합니다! 새로 추가하는 3줄이 위쪽의 `scene:`, `events:` 줄과 시작 위치가 정확히 일치하도록 맞춰주세요.

텍스트 편집기(`nano` 등)에서 변경 사항을 저장(`Ctrl + O` 누르고 `Enter`)하신 뒤, 편집기를 종료(`Ctrl + X`)해 주세요.

---

### 🚀 수정 완료 후 최종 실행 순서

터미널로 돌아오셔서 아래 3가지 명령어를 **순서대로 하나씩** 입력해 주세요.

**1. 파이토치 메모리 최적화 옵션 켜기 (에러 로그 권장 사항)**

```bash
export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True

```

**2. 에러가 나면서 비정상적으로 만들어졌던 찌꺼기 폴더 삭제** (덮어쓰기 에러 방지)

```bash
rm -rf $(pwd)/datasets/so101_teleop_vials

```

**3. 데이터 수집 실행 명령어 입력!**

```bash
lerobot_agent --task Lerobot-So101-Teleop-Vials-To-Rack-DR \
     --repo_id ${HF_USER}/so101_teleop_vials \
     --repo_root $(pwd)/datasets/so101_teleop_vials \
     --task_name "Pick up the vial and place it in the rack"

```

이제 녹화 메모리 버퍼 용량이 절반 이하(약 1GB 내외)로 크게 줄어들었기 때문에, 시뮬레이션 창이 뜨고 `S` 키를 눌러도 튕기지 않고 `[INFO]: Started recording.` 이라는 문구와 함께 부드럽게 녹화가 시작될 것입니다!

(최대 길이를 15초로 설정하셨으므로, `S`를 누르신 직후 로봇 팔을 움직여 15초 이내에 약병을 선반에 꽂는 태스크를 완료하시고 다시 `S`를 눌러 저장해주시면 됩니다.)
