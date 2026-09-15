# 5단계 : Isaac GT00T VLA 모델

### 시뮬레이션 정책 평가

- 터미널 1(real-robot 컨테이너) - GR00T 정책 서버 시작
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

- 평가 모델 설정

```bash
export MODEL=aravindhs-NV/grootn16-finetune_sreetz-so101_teleop_vials_rack_left/checkpoint-10000
```

- 해당 모델로 정책 서버 실행

```bash
python Isaac-GR00T/gr00t/eval/run_gr00t_server.py \
    --model-path /workspace/models/$MODEL
```
