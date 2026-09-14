# 1단계 : 소프트웨어 셋업

### 저장소 복제

git clone https://github.com/isaac-sim/Sim-to-Real-SO-101-Workshop.git
cd Sim-to-Real-SO-101-Workshop

### Teleop and Simulation 컨테이너 도커 빌드

docker build -t teleop-docker -f docker/sim/Dockerfile .

### Ada GPUs, For NVIDIA GPUs based on the Ada architecture (e.g. RTX 4090):

./docker/real/build.sh ada

### 모델 가져오기

```bash
mkdir -p models
```

### 허킹페이스 가입 및 로그인

[https://huggingface.co/](https://huggingface.co/)

huggingface-cli login

- Before downloading from Hugging Face, log in with hf auth login to avoid anonymous rate limits.

### 모델 다운로드 -> 복사 (다운로드 시간 지체)

hf download aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left \
  --local-dir ./models/aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left

hf download aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left_sim_and_real \
  --local-dir ./models/aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left_sim_and_real

hf download aravindhs-NV/sreetz-so101_teleop_vials_rack_left_augment_02 \
  --local-dir ./models/aravindhs-NV/sreetz-so101_teleop_vials_rack_left_augment_02

hf download aravindhs-NV/so100-orig-groot-vials-rack-left-cosmos-70 \
  --local-dir ./models/aravindhs-NV/so100-orig-groot-vials-rack-left-cosmos-70

# 2단계 : 로봇 조정

### 도커 실행

- 새 터미널 오픈 (Ctrl+Alt+T)
- teleop-docker 컨테이너 실행

