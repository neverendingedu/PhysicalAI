# 1단계 : 소프트웨어 셋업

### 저장소 복제

```bash
git clone https://github.com/isaac-sim/Sim-to-Real-SO-101-Workshop.git
cd Sim-to-Real-SO-101-Workshop
```

### Teleop and Simulation 컨테이너 도커 빌드

```bash
docker build -t teleop-docker -f docker/sim/Dockerfile .
```

### Ada GPUs, For NVIDIA GPUs based on the Ada architecture (e.g. RTX 4090):

```bash
./docker/real/build.sh ada
```

### 모델 가져오기

```bash
mkdir -p models
```

### 허킹페이스 가입 및 로그인

[https://huggingface.co/](https://huggingface.co/)

```bash
huggingface-cli login
```

- Before downloading from Hugging Face, log in with hf auth login to avoid anonymous rate limits.

### 모델 다운로드 -> 복사 (다운로드 시간 지체)

```bash
hf download aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left \
  --local-dir ./models/aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left

hf download aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left_sim_and_real \
  --local-dir ./models/aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left_sim_and_real

hf download aravindhs-NV/sreetz-so101_teleop_vials_rack_left_augment_02 \
  --local-dir ./models/aravindhs-NV/sreetz-so101_teleop_vials_rack_left_augment_02

hf download aravindhs-NV/so100-orig-groot-vials-rack-left-cosmos-70 \
  --local-dir ./models/aravindhs-NV/so100-orig-groot-vials-rack-left-cosmos-70
```

# 2단계 : 로봇 조정

### 도커 실행

- 새 터미널 오픈 (Ctrl+Alt+T)
- teleop-docker 컨테이너 실행
```bash
cd ~/Sim-to-Real-SO-101-Workshop
xhost +
docker run --name teleop -it --privileged --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
   -e "PRIVACY_CONSENT=Y" \```
   -e DISPLAY \
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

### 원격 암포트 식별

```bash
lerobot-find-port
```

- USB 포트 빼고 포트 정보 기록. 예) /dev/ttyACM2

- 환경 변수 설정

```text
setenv TELEOP_PORT=/dev/ttyACM # !! make sure to update
setenv TELEOP_ID=orange_teleop # use this line as-is
```
```text
setenv ROBOT_PORT=/dev/ttyACM # !! make sure to update
setenv ROBOT_ID=orange_robot # use this as-is
```
```bash
echo "Teleop port is ${TELEOP_PORT} with id ${TELEOP_ID}"
echo "Robot port is ${ROBOT_PORT} with id ${ROBOT_ID}"
```

- docker 열어 두기 

### 원격(리더) 암 캘리브레이션

```bash
lerobot-calibrate \
    --teleop.type=so101_leader \
    --teleop.port=$TELEOP_PORT \
    --teleop.id=$TELEOP_ID
```
### 팔로워 암 캘리브레이션

```bash
lerobot-calibrate \
    --teleop.type=so101_leader \
    --teleop.port=$TELEOP_PORT \
    --teleop.id=$TELEOP_ID
```

### 캘리브레이션 체크

```bash
python docker/real/scripts/so101_check_calibration.py 
```

# 3단계 : SO-101 조작


# 
