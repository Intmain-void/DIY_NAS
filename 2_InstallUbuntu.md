# 2. Ubuntu 22.04 LTS 설치

처음에는 서버용 20.04를 설치하였지만, CPU 온도를 읽지 못하는 오류가 있었다.

커널 업데이트를 할까 생각했지만, 이번에는 GUI로 설치하려고 한다.

# Ubuntu 설치 디스크 만들기

처음에 맥북에서 balenaEtcher를 통해 만들었지만… 실패하였다!

설치 시간 오래 걸리는게 싫어서 여분 nvme ssd로 부팅 디스크를 만드려고 해서 그런 것 같다.

그래서 윈도우에서 rufus를 이용하여 설치 디스크를 만들었다.

하지만 설치가 되지 않았다. 아래 링크를 확인해보니 Ubuntu 대신 Ubuntu (safe graphics)를 사용해야 정상적으로 설치가 가능하였다.

[레딧 링크](https://www.reddit.com/r/Ubuntu/comments/1507xf0/black_screen_when_installing_ubuntu_2204/)

# 기본적인 프로그램 설치


```bash
sudo apt update
```

```bash
sudo apt upgrade
```

이외에 `vim` 등 기본 프로그램 등을 설치하였다.

# 하드디스크 마운트

4tb 이상 디스크는 그냥 마운트가 불가능. GPT 지원 확인 후 해야 함

## GPT지원 확인

- 커널버전 확인

```bash
uname -r
```

GPT 버전이 3.1 이상이면 사용가능하다.

- GPT 파티션 사용가능여부 확인

```bash
cat /boot/config-{kernel명} | grep EFI | grep -v ^#
```

아래 4개 있는지 확인

```bash
CONFIG_EFI=y
CONFIG_FB_EFI=y
CONFIG_EFI_VARS=y
CONFIG_EFI_PARTITION=y
```

## 드라이브 정보 확인

```bash
sudo fdisk -l
```

## `Parted` 로 파티션 만들기

없으면 `sudo apt install parted` 로 깔기

- 파티션 만들기

```bash
sudo parted /dev/sda # sda는 바뀔 수 있음. sudo fdisk -l로 확인

mklabel gpt

unit TB

mkpart primary 0.00TB 8.00TB

print

q
```

- 디스크 포맷

```bash
sudo mkfs.ext4 /dev/sda1
```

## HDD 마운트하기

- 마운트할 디렉터리 생성

```bash
sudo mkdir /mnt/hdd1
```

- 연결

```bash
# mount 하드경로 디렉터리경로
sudo mount /dev/sda /mnt/hdd1
```

## 시스템에서 항상 마운트되게 연결

- 디스크 UUID 확인

```bash
sudo blkid
```

- fstab 수정: 부팅 시 자동 마운트

```bash
sudo vim /etc/fstab
```

맨 밑에 추가

```bash
# UUID="{하드UUID}" 마운트경로 ext4 defaults 0 0
UUID="UUID=2968a0cd-a585-4209-af44-b96c4c50e6a9" /mnt/hdd1 ext4 defaults 0 0
```

- 마운트

```bash
sudo mount -a
```

출처:

[https://makeutil.tistory.com/226](https://makeutil.tistory.com/226)

[https://velog.io/@jhhwghg9911/UBUNTU-HDD-마운트하기](https://velog.io/@jhhwghg9911/UBUNTU-HDD-%EB%A7%88%EC%9A%B4%ED%8A%B8%ED%95%98%EA%B8%B0)
