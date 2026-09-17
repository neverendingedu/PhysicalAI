# 6단계 : 실제 평가 

## 실제 로봇 정책 평가

### 터미널 1(real-robot 컨테이너) - GR00T 정책 서버 시작
- 새 터미널 시작

```bash
cd ~/Sim-to-Real-SO-101-Workshop
xhost +
docker run -it --rm --name real-robot --network host --privileged --gpus all \
    -e DISPLAY \
    -v /dev:/dev \
    -v /run/udev:/run/udev:ro \
    -v $HOME/.Xauthority:/root/.Xauthority \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -v ~/.cache/huggingface/lerobot/calibration:/root/.cache/huggingface/lerobot/calibration \
    -v ./docker/env:/root/env \
    -v ~/models:/workspace/models \
    -v $(pwd)/docker/real/scripts:/Isaac-GR00T/gr00t/eval/real_robot/SO100 \
    real-robot \
    /bin/bash
```

- cuda 버전 문제가 있을 경우

```bash
cd ~/Sim-to-Real-SO-101-Workshop
xhost +
docker run -it --rm --name real-robot --network host --privileged --gpus all \
    -e DISPLAY=$DISPLAY \
    -e NVIDIA_DISABLE_REQUIRE=1 \
    -v /dev:/dev \
    -v /run/udev:/run/udev:ro \
    -v $HOME/.Xauthority:/root/.Xauthority \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -v ~/.cache/huggingface/lerobot/calibration:/root/.cache/huggingface/lerobot/calibration \
    -v ./docker/env:/root/env \
    -v ~/models:/workspace/models \
    -v $(pwd)/docker/real/scripts:/Isaac-GR00T/gr00t/eval/real_robot/SO100 \
    real-robot \
    /bin/bash

# 1. Error 804 방지를 위해 호환성(compat) 폴더 강제 삭제
rm -rf /usr/local/cuda/compat /usr/local/cuda-*/compat

# 2. 라이브러리 목록 새로고침
ldconfig
```

- 평가 모델 설정

```bash
export MODEL=aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left/checkpoint-10000
```

- 해당 모델로 정책 서버 실행

```bash
python Isaac-GR00T/gr00t/eval/run_gr00t_server.py \
    --model-path /workspace/models/$MODEL
```

### 터미널 2(real-robot 컨테이너) - 평가

- 새 터미널 시작

```bash
docker exec -it real-robot /bin/bash
```

- 평가 스크립트 실행
  
```bash
python Isaac-GR00T/gr00t/eval/real_robot/SO100/so101_eval.py \
  --robot.type=so101_follower \
  --robot.port="$ROBOT_PORT" \
  --robot.id="$ROBOT_ID" \
  --robot.cameras="{
      wrist:  {type: opencv, index_or_path: $CAMERA_GRIPPER, width: 640, height: 480, fps: 30},
      front:  {type: opencv, index_or_path: $CAMERA_EXTERNAL, width: 640, height: 480, fps: 30}
  }" \
  --policy_host=localhost \
  --policy_port=5555 \
  --lang_instruction="Pick up the vial and place it in the yellow rack" \
  --rerun True
```

