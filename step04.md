# 4단계 : Sim-to-Real

### 시뮬레이션 환경

```bash
cd ~/Sim-to-Real-SO-101-Workshop
xhost +
docker run --name teleop -it --privileged --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
   -e "PRIVACY_CONSENT=Y" \
   -e DISPLAY=$DISPLAY \
   -v /tmp/.X11-unix:/tmp/.X11-unix \
   --shm-size=16gb \
   -v /dev:/dev \
   -v /run/udev:/run/udev:ro \
   -v $HOME/.Xauthority:/root/.Xauthority \
   -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
   -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
   -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
   -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
   -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
   -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
   -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
   -v ~/docker/isaac-sim/documents:/root/Documents:rw \
   -v ~/.cache/huggingface/lerobot/calibration:/root/.cache/huggingface/lerobot/calibration \
   -v ./docker/env:/root/env \
   -v $(pwd)/source:/workspace/Sim-to-Real-SO-101-Workshop/source \
   -v $(pwd)/outputs:/workspace/Sim-to-Real-SO-101-Workshop/outputs \
   -v $(pwd)/datasets:/workspace/Sim-to-Real-SO-101-Workshop/datasets \
   -v $(pwd)/docker/real/scripts:/workspace/Sim-to-Real-SO-101-Workshop/docker/real/scripts \
   teleop-docker:latest
```

### 환경 변수 최종 설정 (예시)

```text
setenv TELEOP_PORT=/dev/ttyACM0 # !! make sure to update
setenv TELEOP_ID=orange_teleop # use this line as-is
setenv ROBOT_PORT=/dev/ttyACM1 # !! make sure to update
setenv ROBOT_ID=orange_robot # use this as-is
setenv CAMERA_GRIPPER=2 # make sure to update to your values
setenv CAMERA_EXTERNAL=0 # make sure to update to your values
```

### usd 파일 얻기

- Git LFS 설치 및 활성화

```bash
sudo apt-get update
sudo apt-get install -y git-lfs
git lfs install
```

- 작업 폴더로 이동 후 실제 3D 모델 파일 당겨오기

```bash
cd ~/Sim-to-Real-SO-101-Workshop
git lfs pull
```

(명령어를 입력하면 수 MB ~ 수십 MB 크기의 .usd 파일들이 주르륵 다운로드됩니다.)

- 파일이 정상적으로 받아졌는지 크기 확인

```bash
ls -lh source/sim_to_real_so101/assets/usd/SO-ARM101-USD.usd
```

출력 결과에 파일 용량이 메가바이트(MB) 단위로 나오면 완벽하게 성공입니다. (만약 130바이트 등 아주 작게 나온다면 아직 LFS 다운로드가 안 된 것입니다.)

### 시뮬레이션 환경 Isaac Lab

- 도메인 무작위화 활성화

```bash
lerobot_agent --task Lerobot-So101-Teleop-Vials-To-Rack-DR
```

- 도메인 무작위화 비활성화

```bash
lerobot_agent --task Lerobot-So101-Teleop-Vials-To-Rack
```

### 데모 데이터 녹화하기 

- Hugging face 서용자명 설정

```bash
export HF_USER=your-hf-username
```

- 녹화 세션 시작

```bash
lerobot_agent --task Lerobot-So101-Teleop-Vials-To-Rack-DR \
    --repo_id ${HF_USER}/so101_teleop_vials \
    --repo_root $(pwd)/datasets/so101_teleop_vials \
    --task_name "Pick up the vial and place it in the rack"
```

- 녹화 시작/중지 : S
- 녹화 취소 : C
- 녹화 환경 초기화 : R
- 프로그램 종료 : Ctrl + C

### 수집된 데이터 검토

```bash
lerobot-dataset-viz \
    --repo-id ${HF_USER}/so101_teleop_vials \
    --root $(pwd)/datasets/so101_teleop_vials \
    --episode-index 0
```

- episode-index 번호를 변경해서 여러 에피소드 검토 
