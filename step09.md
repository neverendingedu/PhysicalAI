# 9단계 : SAGE(Sim-to-Real Actuation Gap Estimation)

### SAGE 시작

- SAGE 저장소 클론

```bash
git clone https://github.com/isaac-sim2real/sage.git
cd sage
```

- SAGE 컨테이너 시작

```bash
xhost +
docker run --name isaac-lab --entrypoint bash -it --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
   -e "PRIVACY_CONSENT=Y" \
   -e DISPLAY \
   -v /tmp/.X11-unix:/tmp/.X11-unix \
   -v $HOME/.Xauthority:/root/.Xauthority \
   -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
   -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
   -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
   -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
   -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
   -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
   -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
   -v ~/docker/isaac-sim/documents:/root/Documents:rw \
   -v $(pwd):/app:rw \
   sage
```

- 시뮬레이션 데이터 수집 실행

```bash
${ISAACSIM_PATH}/python.sh scripts/run_simulation.py \
    --robot-name so101 \
    --motion-source custom \
    --motion-files motion_files/so101/custom/pick_place.txt \
    --valid-joints-file configs/so101_valid_joints.txt \
    --output-folder output \
    --fix-root \
    --physics-freq 200 \
    --render-freq 200 \
    --control-freq 50 \
    --kp 100 \
    --kd 2
```

### 실제 로봇 데이터 수집

- conda 환경 생성

```bash
conda create -y -n lerobot python=3.10
conda activate lerobot
```

- 패키지 설치

```bash
conda install ffmpeg -c conda-forge  # optional
pip install 'lerobot[feetech]'
```

- 도커 설치

```bash
docker build -f Dockerfile_so101_real -t sage:so101 .
```

- 컨테이너 실행

```bash
docker run -it --rm --privileged \
    -v $HOME/.cache/huggingface:/root/.cache/huggingface \
    -v /dev:/dev \
    -v $(pwd):/app \
    sage:so101
```

- 모션 실행과 데이터 수집

```bash
sudo chmod 666 /dev/ttyACM*  # Skip this step if running in Docker container as root
# Modify the optional arguments as needed
python scripts/run_real.py \
    --robot-name=so101 \
    --motion-files=custom/custom_motion.txt \
    --output-folder=output \
    --robot-port=/dev/ttyACM0 \
    --robot-type=so101_follower \
    --robot-id=my_awesome_follower_arm
```

- 갭(Gap) 분석

```bash
python scripts/run_analysis.py \
    --robot-name so101 \
    --motion-source custom \
    --motion-names "pick_place" \
    --output-folder output \
    --valid-joints-file configs/so101_valid_joints.txt
```

### GapONet 학습

```bash
python scripts/rsl_rl/train.py --task Isaac-Humanoid-Operator-Delta-Action \
  --num_envs=4080 --max_iterations 100000 --experiment_name Sim2Real \
  --letter amass --run_name delta_action_mlp_payload --device cuda env.mode=train --headless
```

### 평가 및 내보내기

- 체크포인트 평가

```bash
python scripts/rsl_rl/play.py --task Isaac-Humanoid-Operator-Delta-Action \
   --model ./model/model_17950.pt --num_envs 20 --headless
```

- Isaac Sim 없이 가벼운 추론을 위해 JIT로 내보내기

```bash
python scripts/rsl_rl/inference_jit.py \
    --export \
    --checkpoint ./model/model_17950.pt \
    --task Isaac-Humanoid-Operator-Delta-Action \
    --output ./model/policy.pt \
    --device cuda:0 \
    --num_envs 20
```

- 테스트 데이터에서 추론 (Isaac Sim 불필요)

```bash
python scripts/rsl_rl/deploy.py \
    --model ./model/policy.pt \
    --test_data ./source/sim2real/sim2real/tasks/humanoid_operator/motions/motion_amass/edited_27dof/test.npz
```
