# RAID

## 개념

* 여러 개의 디스크를 하나의 디스크 처럼 관리할 수 있는 기능

### 하드웨어 RAID

* 하드웨어 레벨에서 제조 업체가 여러 개의 디스크를 연결한 장비를 만들어 공급하는 것 혹은 연결하는 것
* 안정적이고 제조 업체의 기술지원을 받을 수 있지만 가격이 비싸고 제조 업체마다 조작 방법 상이
* 고가의 경우 SA-SCSI 디스크로, 중저가의 경우 SATA 디스크로 만듦

### 소프트웨어 RAID

* 운영체제 안에서 구현되어 디스크를 관리한다
* 하드웨어 RAID와 비교하면 신뢰성, 속도가 낮지만 적은 비용으로 안전하게 데이터 저장 가능

## RAID 레벨

### RAID 구성 방식

<figure><img src=".gitbook/assets/Screenshot 2023-12-05 at 23.46.46.png" alt=""><figcaption></figcaption></figure>

* 단순 볼륨 (레이드가 아님)
* **Linear RAID**
  * 앞 디스크에 데이터를 모두 저장한 후 다음 디스크에 저장
* **RAID 0**
  * 모든 디스크를 병렬적으로 사용 (분산해서 저장)

#### Linear RAID와 RAID 0 비교

RAID 0은 여러 개의 디스크에 동시로 저장하는 방법인 스트라이핑 방식으로, 12바이트를 저장하는 경우 Linear RAID는 한 곳에 12바이트를 순차적으로 쓰는 반면에 RAID 0는 4바이트씩 써서 속도가 더 빠르다.

Linear RAID는 한 디스크 당 공간 효율성이 좋지만 속도가 느려 안정성에 초점을 맞출 때 적합하고,

RAID 0는 속도가 빠른 대신에 디스크가 하나만 고장나도 전체 데이터의 무결성을 보장하지 못해 속도에 초점을 맞출 때 적합하다.&#x20;

*   **RAID 1**

    미러링 개념으로, 똑같은 데이터를 각 디스크 저장한다. 공간 효율성이 50%에 달하고, 중요 데이터를 저장하기에 적당한 방식이다. 데이터를 이중화하므로 안정성이 매우 높다.
* RAID 2
* RAID 3
* RAID 4
*   **RAID 5**

    네트워크에서 오류 처리를 하는 것 처럼 데이터 중간에 패리티 비트를 삽입하고, 디스크에 오류가 발생하면 패리티 비트를 이용해 데이터를 복구할 수 있다. 예를 들어, 짝수 패리티의 경우 00111의 이진 데이터는 1이 홀수개이므로 1을 추가해 짝수개가 되게 한다.

    최소 세개 이상의 디스크가 필요하며, 디스크 중 하나가 고장나도 복원이 가능하다.

    어느 정도의 결성을 허용하고 저장 공간의 효율성도 뛰어나다는 것이 장점이다.
*   RAID 6

    RAID 5에서 패리티를 두개 이용해 처리하게 된다.

## 관련 커맨드

* 디스크 리스트 조회: `ls -l /dev/sd*`
* 파티션 생성: `fdisk {disk_path}` (RAID로 묶을 파티션들 모두 생성)
  * 파일 시스템으로 `fd` 부여
* RAID 관리 패키지 설치: `apt -y install mdadm`&#x20;
* 파티션 상태 확인: `fdisk -l {disk_path}`
* RAID 구축: `mdadm --create {target_path} --level={raid_level} --raid-devices={device_number} {partition_paths}`
* 파일 시스템 생성/마운트
  * 파일 시스템 생성
    * `mkfs -t {file_system} {partition_device}`
      * 예: `mkfs -t ext4 /dev/sdb1`
    * `mkfs.{file_system} {partition_device}`
      * 예: `mkfs.ext4 /dev/sdb1`
  * 파일 시스템 마운트
    * `mount {partition_device} {path_to_mount}`
* RAID 확인: `mdadm --detail {partition_device}`
* RAID 구성 해제: `mdadm --stop /dev/md*`
* RAID 버그 방지: `/etc/mdadm/mdadm.conf` 파일에 `mdadm --detail --scan` 명령의 결과 에서 name을 제거하고 삽입
