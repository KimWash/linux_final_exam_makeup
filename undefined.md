# 리눅스 디스크 관리

### 파티션 생성하기

* 단순 파티션 만들기
  * `fdisk {disk_path}`&#x20;
    * 디스크 파티션 기능을 제공하는 커맨드라인 유틸리티로, 여러 커맨드를 이용해 디스크를 관리할 수 있다. 예: `fdisk /dev/sdb`
    * 커맨드
      * n: 새로운 파티션 분할
      * p: 설정 내용 확인
      * w: 설정 내용 저장
* 파일 시스템 생성
  * `mkfs -t {file_system} {partition_device}`
    * 예: `mkfs -t ext4 /dev/sdb1`
  * `mkfs.{file_system} {partition_device}`
    * 예: `mkfs.ext4 /dev/sdb1`
* 파일 시스템 마운트하기
  * `mount {partition_device} {path_to_mount}`
* 파일 시스템 마운트 해제하기
  * `umount {partition_device}`
*   자동 마운트 세팅하기

    * `/etc/fstab` 파일의 마지막 줄에 다음 내용 추가

    `{partition_device} {path_to_mount} {file_system} {option} {dump} {pass}`

### 공간 할당

1.  옵션 부여

    디스크가 꽉 찼을 때 시스템 전체가 가동되지 않는 문제를 해결하기 위해 쿼터 개념을 이용해 각 사용자가 사용할 수 있는 파일의 용량을 제한하게 된다. 아래 옵션들을 `/etc/fstab` 파일의 해당 파티션 부분에 추가한다.

    * `usrjquota=aquota.user`
    * `jqfmt=vfsv0`

    추가로, 설정을 재부팅 없이 반영하기 위해 `mount --options remount {path_to_mount}` 명령을 이용해 다시 마운트한다.
2. 쿼터 DB 생성
   * quota 패키지 설치: `apt -y install quota`
   * ```bash
     cd {path_to_mount}
     quotaoff -avug
     quotacheck -augmn
     rm -f aquota.*
     quotacheck -augmn
     touch aquota.user aquota.group
     chmod 600 aquota.* 
     quotacheck -augmn 
     quotaon -avug
     ```
3. 사용자별로 공간 할당
   * `edquota -u {user_name}` 명령으로 사용자에 대해 파티션별 할당량 편집
   * `quota` 명령을 이용해 사용자에게 할당된 디스크 공간 확인
     * grace 열을 이용해 할당된 공간이 언제까지 이용 가능한지 확인 가능
   * `repquota {partition_device}` 명령으로 사용자별 현재 사용량 확인
   * `edquota -p {original_user} {target_user}`

### 디스크 관련 용어

* Filesystem
  * 사용자별 쿼터를 할당하는 파일 시스템 ex) `/dev/sdb1`
* blocks: 현재 사용자가 사용하는 블록 크기. (kb단위)
* soft/hard: 소프트/하드 사용 한도. 0이면 제한이 없음을 의미.
* inodes: inode의 개수를 의미.
