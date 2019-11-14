# BootProcess
## 요약
ONL 을 위한 부트 프로세스는 3 단계로 구성된다.
1. uBoot 단계
2. ONL 로더 단계
3. ONL OS  
## 상세 부트 프로세스
1. uBoot 는 최초의 부트 로더 임
2. uBoot 는 플래시로 부터 ‘nos\_ bootcmd’  [^1]환경 변수를 읽고, 이를 수행한다.
3. 만약 $nos\_boot\_cmd 가 리턴 되면, uBoot 는 ONL Installer 를 다운로드하고 ONL 로더를 설치하기 위한 ONIE 를 동작 시킨다.
	1. 공장 초기 $nos\_boot\_cmd 는 즉시 리턴되는 명령어(e.g., 'echo’)를 사용
4. 일반 동작(ONIE 동작)에서 $nos\_boot\_cmd 는 ONL Loader 를 불러오고 동작 시킨다.
5. ONL loader 는 자신의 Linux kernel("boot kernel”)을 부팅한다. 
6. ONL loader 는 파일 내의 URL(URL=cat /etc/SWI) 에 기반하여 어느 SWI 를 실행할지 결정한다.
7. ONL loader 는  /bin/boot $URL 을 실행한다.
8. ONL loader 는 SWI 파일을 검색하여 다음 동작을 수행한다. 
	1. 만약 원격 URL(e.g., http://, ftp://, etc.)이면, /mnt/flash2 에 SWI 파일이 있는지 확인하고 없다면 다운로드 한다. 
	2. 만약 로컬 URL 이면 디바이스에 접속 가능한지 확인한다.
	3. 만약 ZTN(Zero Touch Networking) URL 이면, ZTN 프로토콜을 수행하여 SWI을 받는다.
9. ONL loader 는 SWI 의 'rootfs' 파일을 읽고,  overlayfs 를 사용해서 'rootfs' 를 마운트 한다.
10. ONL loader 는 SWI 내의 커널로 전환하기 위한  kexec() 를  수행하고, main ONL kernel 을 부팅한다.
11. 특정 플랫폼과 관련된 것을 불러오기 위해, ONL kernel 는 커널 매개 변수로써 ONIE platform identifier 를 전달한다.

## 파티션 레이아웃
스위치는 통상적으로 2개의 플래쉬(Flash) 저장장치를 가진다.
1. Smaller Boot Flash(ex. 64MB flash)
	1. Partition 1:  uBoot Partition
	2. Partition 2: environmental variables (e.g., $nos\_boot\_cmd) 
	3. Partition 3: ONIE Partition
	4. Partition 4+: Free space (unused)
2. Mass Storage Device(ex. Compact flash, 2+GB)
	1. Partition 1: ONL loader kernel -- 해당 파티션의 포멧은 특정 플랫폼에서 uBoot 가 어떤 포멧을 지원하느냐 에 따라 달라진다.
	2. Partition 2: ONL Loader configuration 파일 ( "/mnt/flash" 에 마운트됨)
	3. Partition 3: ONL SWitch Images (SWIs) partition ( "/mnt/flash2" 에 마운트 됨)

## ONL 파일 시스템 레이아웃
	root@onl-powerpc:/bin# df
	Filesystem     1K-blocks  Used Available Use% Mounted on
	rootfs             72040   176     71864   1% /
	devtmpfs            1024     0      1024   0% /dev
	none               72040   176     71864   1% /
	tmpfs              48028   148     47880   1% /run
	tmpfs               5120     0      5120   0% /run/lock
	/dev/sda2          71177     7     71170   1% /mnt/flash
	/dev/sda3        3791960 98172   3693788   3% /mnt/flash2
	tmpfs              96040     0     96040   0% /run/shm

## SWI
	robs@ubuntu:~/work.onl/ONL/builds/swi/powerpc/all$ unzip -l onl-c7850a5-powerpc-all-2014.02.12.11.49.swi
	Archive:  onl-c7850a5-powerpc-all-2014.02.12.11.49.swi
	Length      Date       Time    Name
	---------   ---------- -----   ----
	6877424     2014-02-12 11:55   kernel-85xx
	3378828     2014-02-12 11:55   initrd-powerpc
	93753344    2014-02-12 11:55   rootfs-powerpc.sqsh
	100         2014-02-12 11:55   version
	---------                     -------
	104009696                     4 files
Zip 파일은 다음을 포함한다.
1. 'kernel-85xx' : 동작하는 ONL 의 실제 커널 이미지
2. 'initrd-$ARCH' : 커널을 위한 초기 램 디스크
3. 'rootfs-$ARCH' : 동작하는 ONL 을 위한 Root 파일 시스템
4. 'version' : 빌드 버전 스트링 (형식 -  "Open Network Linux $shorthash ($target,$date,$githash)”)

[^1]:	nos(Network Operating System)