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

### 시뮬레이션 환경 Isaac Lab

- 도메인 무작위화 활성화

```bash
lerobot_agent --task Lerobot-So101-Teleop-Vials-To-Rack-DR
```

- 도메인 무작위화 비활성화

```bash
lerobot_agent --task Lerobot-So101-Teleop-Vials-To-Rack
```
