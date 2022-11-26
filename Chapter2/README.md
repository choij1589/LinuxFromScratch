# Chapter 2. Preparing the Host System
### 2.1 Introduction
이 장에서는 LFS를 빌드하기 전 필요한 툴들이 호스트 머신에 있는지 체크하고 없는 툴들을 설치한다. 또한, LFS시스템을 위한 파티션을 생성한다. 마지막으로, 생성한 파티션 위에 파일 시시템을 올리고 호스트 머신에 마운트 한다.

---
### 2.2 Host System Requirements
[version-check.sh](https://github.com/choij1589/LinuxFromScratch/blob/main/Chapter2/version-check.sh) 를 이용하여 host machine에 툴들이 있는지 검사한다.
>  Host machine을 Windows나 Mac OS를 사용하는 것도 가능할 것 같긴 한데, 여러 문제들을 마주칠 가능성이 높다. 최대한 Ubuntu나 Fedora같은 범용 OS를 사용하자. 나는 Fedora 36에서 LFS를 빌드하고 있다.

다음은 스크립트를 돌린 결과이다.
![[version_check.png]]

---
### 2.3 Building LFS in Stages
LFS는 한번에 빌드를 완료하지 않는다. 작업을 중간부터 시작할 때, 다음을 확인한 후 시작하자.
- Chapter 1-4: Host machine에서 작업한다. root user로 작업하며, LFS 환경변수가 적용되어있어야 한다.
- Chapter 5-6: /mnt/lfs partition이 항상 마운트 되어 있어야 한다. 또한, lfs user인 상태로 작업을 진행해야 한다. su -lfs를 이용해 superuser 권한을 주도록 하자.
- Chapter 7-10: /mnt/lfs partition이 항상 마운트 되어 있어야 한다. "Changing Ownership" 부터 "Entering the CHroot Environment" 는 root user로 진행하며, LFS 환경변수가 적용되어 있어야 한다(이후 이 환경변수는 사용하지 않는다).
> Section 7.3.1 "Mounting and Population /dev" 와 Section 7.3.2 "Mounting Virtual Kernel Systems"에서qnxj virtual file system을 마운트 하라는데, 아직은 무슨 말인지 모르겠다.

---
### 2.4 Creating a New Partition
다른 Linux 배보판과 마찬가지로, LFS도 지정된 파티션에 설치가 진행된다. 어떻게 파티션을 나눌지에 대한 황금 룰은 없다. 본인이 어떤 목적의 시스템을 만들지 먼저 결정하고 어떻게 파티션을 분할할지 생각해 보자.
다음은 각 파티션 별 용도를 정리한 것이다. 필수 파티션은 하이라이트, 권장 파티션은 LFS, BLFS, thin client별로 정리해 두었다.
1. **The Root Partition**
	20GB면 넉넉하다. 모든 Linux의 시작은 당연히 /에서 시작한다.
2. **The Swap Partition**
	RAM 용량이 부족할 경우 임시로 실행 파일을 로드한다. SSD를 이용해 swap 파티션을 생성하는건 좋지 않다고 한다(왜?). 보통 RAM용량의 2배로 설정하지만, 충분한 RAM용량을 가지고 있다면 너무 크게 만들 필요는 없는 것 같다.
3. **The Grub Bios Partition**
	boot disk가 GPT로 partiton 되어 있으면 1MB정도의 grub bios partition이 필요하다고 한다(뭔말이지...). fdisk를 사용하면 "BIOS Boot"라고 표시되어 있는 걸 볼 수 있다.
4. **/boot**
	Kernel과 booting information을 저장한다. 있으면 좋겠죠? 200MB면 충분하다.
5. /boot/efi (for BLFS)
	UEFI로 시스템을 부팅한다면 필요하다.
6. **/home**
	여러 유저간 또는 배포판간 파일을 공유할 때 유용하다. 클수록 좋다.
7. /usr (for thin client)
	LFS build에 꼭 필요하진 않다. 시스템을 실행하기 위한 모든 binary파일들은 /usr에 저장하고, /bin, /lib, /sbin은 /usr에 symlink한다. 이 경우 root 파티션의 크기는 아주 작아질 수 있다.
8. /opt (for BLFS)
	Gnome이나 KDE같은 큰 패키지를 설치할 때 사용한다. 5~10GB정도의 크기가 필요하다.
9. /tmp (for thin client)
10. /usr/src (for BLFS)
	BLFS를 빌드하기 위한 소스 파일을 저장하기 유용한 장소다. 30-50GB의 크기가 필요하다.
> TODO: 파티션을 어떻게 나눌 것인지?
> TODO: fdisk 사용법 정리 (in Appendix)
