---
description: 'SQL server 재설치, 방화벽 설정, failover cluster feature 설치, domain join'
---

# 3. SQL Server 설치

## SQL Server 기본 인스턴스 삭제 

sqlvm-primary, sqlvm-secondary 두 서버 모두 기본 인스턴스 삭제 

![](../../../.gitbook/assets/dcvm-dc-install23.png)

Remove 클릭 후 기본 인스턴스 삭제 

![](../../../.gitbook/assets/dcvm-dc-install24.png)

Database Engine Services 체크 후 삭제 

![](../../../.gitbook/assets/dcvm-dc-install25.png)

삭제 완료 

![](../../../.gitbook/assets/dcvm-dc-install26.png)

## 방화벽 오픈 

sqlvm-primary, sqlvm-secondary 두 서버 모두 인바운드 방화벽 포트 오픈 

* SQL Server VM port 1433
* Azure 부하 분산 장치 프로브 59999
* Database mirroring 5022

![](../../../.gitbook/assets/dcvm-dc-install27.png)

## Failover Clustering Feature 설치

sqlvm-primary, sqlvm-secondary 두 서버 모두 ​failover clustering feature 설

Add roles and features 클릭

![](../../../.gitbook/assets/dcvm-dc-install28_1.png)

다음

![](../../../.gitbook/assets/dcvm-dc-install29.png)

다음

![](../../../.gitbook/assets/dcvm-dc-install30.png)

다음 

![](../../../.gitbook/assets/dcvm-dc-install31.png)

Failover Clustering 체크 

![](../../../.gitbook/assets/dcvm-dc-install32.png)

Install 

![](../../../.gitbook/assets/dcvm-dc-install33.png)

설치 완료 후 재부팅

![](../../../.gitbook/assets/dcvm-dc-install34.png)

## Domain Join 

sqlvm-primary, sqlvm-secondary 두 서버 모두 도메인 가입

### 도메인 가입전 Azure Portal NIC DNS 설정

sqlvm-primary DC 서버인 10.0.0.4 IP 추가 

![](../../../.gitbook/assets/dcvm-dc-install35.png)

sqlvm-secondary DC 서버인 10.0.0.4 IP 추가 

![](../../../.gitbook/assets/dcvm-dc-install36.png)

sqlvm-primary RDP 접속 후 도메인 가입

![](../../../.gitbook/assets/dcvm-dc-install37.png)

sqlvm-secondary RDP 접속 후 도메인 가입

![](../../../.gitbook/assets/dcvm-dc-install38.png)

도메인 가입 후 재부팅하여 agdb\clooadmin의 도메인 계정으로 접속 

{% hint style="info" %}
Azure Portal에서 IP DNS 설정 후 도메인 가입 오류가 발생할 수 있음 -&gt; 재부팅 후 정상 작
{% endhint %}

## SQL Server 재설치 

SQL 설치미디어로 인스턴스 재설치

sqlvm-primary, sqlvm-secondary 모두 agdb\clooadmin 도메인 계정으로 접속 후 개별 설

![](../../../.gitbook/assets/sqlvm-primary-reinstall.png)

SQL 설치미디어 실행 후 install

![](../../../.gitbook/assets/sqlvm-primary-reinstall2.png)

다음

![](../../../.gitbook/assets/sqlvm-primary-reinstall3.png)

다음

![](../../../.gitbook/assets/sqlvm-primary-reinstall4.png)

라이센스 승인 체크 후 다

![](../../../.gitbook/assets/sqlvm-primary-reinstall5.png)

Database Engine만 선택해도 되나 필요한 feature가 있으면 같이 설치, 다

![](../../../.gitbook/assets/sqlvm-primary-reinstall6.png)

인스턴스명 AGNODE로 명명, 다

![](../../../.gitbook/assets/sqlvm-primary-reinstall7.png)

SQL Server Agent, SQL Server Database Engine의 계정을 서비스 도메인 계정으로 설정, 여기서는 별도의 서비스 계정을 생성하지 않고 admin 계정인 agdb\clooadmin으로 진행

![](../../../.gitbook/assets/sqlvm-primary-reinstall8.png)

서비스 계정과 암호를 입력 후 Startup Type은 SQL Server Agent만 Automatic으로 바꿔줌 

아래 볼륨 관리 권한 체크박스는 장애조치클러스터\(WSFC\) 사용시 각 노드에 개별적으로 구성된다고 하는데 일단 체크.. 볼륨 관리 권한 체크시 성능 향상에 도움이 된다고 

![](../../../.gitbook/assets/sqlvm-primary-reinstall10.png)

Collation 선택, 다

![](../../../.gitbook/assets/sqlvm-primary-reinstall11.png)

SA 계정 혼합 모드 사용 및 WINDOWS AD 계정 추가

![](../../../.gitbook/assets/sqlvm-primary-reinstall12.png)

Data 디렉토리 구

![](../../../.gitbook/assets/sqlvm-primary-reinstall13.png)

TempDB 디렉토리 확인 , 다음 

![](../../../.gitbook/assets/sqlvm-primary-reinstall14.png)

Install

![](../../../.gitbook/assets/sqlvm-primary-reinstall15.png)

설치 완료 후 SQL Server Configure Manager에서 네트워크 동적 포트를 정적 1433으로 변경 

![](../../../.gitbook/assets/sqlserver_conf_port_check.png)

![](../../../.gitbook/assets/sqlserver_conf_port_check2.png)

{% hint style="info" %}
간혹 포트로 인해 접속이 안되는 경우 발생, 꼭 확인하자.. 
{% endhint %}

