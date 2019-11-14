# BuildProcess
## 요약
ONL 빌드 시 다음 2개의 바이너리가 생성된다.
- ONL Installer(ONIE)
- ONL Switch Image(SWI)

코드 재사용을 최대화 하기 위해서, 2개의 바이너리는 다수의 코드와 인스톨 스크립트들을 공유한다. 
- Modules: 라이브러리나 바이너리를 생성하기 위한 소스코드(C, Header, 등등) 모움
- Components: Debian .deb 파일을 생성하기 위한 라이브러리나 바이너리의 모움
- Module 과 Component 들은 다수의 git 저장소에 흩어져 있으며, git submodule 을 사용하여 각 케이스 별로 서로를 참조한다.
- SWI 파일은 root 파일 시스템 이미지와 다수의 패키지가 설치된 모든 Component 를 포함한 커널로 구성되어 있다.
- 인스톨러는 ONL Loader 에 SWI 를 포함 시킨 ONIE 이다.  

## 인스톨러 빌드(ONL Loader 포함)
	* uses busybox and buildroot

## SWI 빌드
	* "multistrap" cross-architecture installer 를 사용
	* 대부분의 작업은 다음을 통해 수행된다:
	    * The `onl-mkws` command
	    * The $ONL/make/swi.mk Makefile
	* Install 을 위한 특정 패키지 리스트는 다음에서 확인할 수 있다:
	    * $ONL/build/swi/$ARCH/all/rootfs/repo.{all,$ARCH}

## 일반
	* buildroot(installer) 혹은 multistrap (SWI)을 사용할 때, temporary root file system 내에 있는 모든 .deb 파일들을 설치
	* $ARCH 의 바이너리들을 QEMU 를 사용하여 _native_ 바이너리 처럼 동작시키기 위해 binformats 을 설정
