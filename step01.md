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

1) 링크 다운로드 및 해제

[공류 링크](https://drive.google.com/file/d/1t4b3AmAVEabQY-3_pJXHyHDJ15IzYHVt/view?usp=sharing)

```bash
cd ~/Sim-to-Real-SO-101-Workshop
tar xzvf models.tar.gz
```

2) 직접 다운로드 

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

