# BootProcess
## 요약
ONL 을 위한 부트 프로세스는 3 단계로 구성된다.
1. uBoot 단계
2. ONL 로더 단계
3. ONL OS  
## 상세 부트 프로세스
1. uBoot 는 최초의 부트 로더 임
2. uBoot 는 플래시로 부터 ‘nos\_ bootcmd’  [^1]환경 변수를 읽고, 이를 수행한다.
3. ..

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