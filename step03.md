
# 3단계 : SO-101 조작

### 원격 조작

```bash
lerobot-teleoperate \
    --robot.type=so101_follower \
    --robot.port=$ROBOT_PORT \
    --robot.id=$ROBOT_ID \
    --teleop.type=so101_leader \
    --teleop.port=$TELEOP_PORT \
    --teleop.id=$TELEOP_ID
```

- 중단 : Ctrl + C

### 카메라 정보 설치 

- v4l2-ctl 도구 설치

```bash
sudo apt install v4l-utils
```

- 연결 비디오 장치 목록 확인

```bash
v4l2-ctl --list-devices
```

### 카메라 종류 확인

```bash
sudo apt install ffmpeg -y
ffplay /dev/video2
```

- video2/4/6 등 숫자를 바꾸면서 확인

### 카메라 설정

```bash
lerobot-find-cameras opencv
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

### 카메라와 함꼐 원격 조작 실행하기 

```bash
lerobot-teleoperate \
  --robot.type=so101_follower \
  --robot.port=$ROBOT_PORT \
  --robot.id=$ROBOT_ID \
  --teleop.type=so101_leader \
  --teleop.port=$TELEOP_PORT \
  --teleop.id=$TELEOP_ID \
  --display_data=true \
  --robot.cameras='{
    "wrist": {
      "type": "opencv",
      "index_or_path": '"$CAMERA_GRIPPER"',
      "width": 640,
      "height": 480,
      "fps": 30
    },
    "front": {
      "type": "opencv",
      "index_or_path": '"$CAMERA_EXTERNAL"',
      "width": 640,
      "height": 480,
      "fps": 30
    }
  }'
```
