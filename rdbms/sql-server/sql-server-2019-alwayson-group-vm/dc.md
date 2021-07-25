---
description: Azure DomainController VM 배포 및 설치
---

# 2. Domain Controller 설치

## Azure Portal에서 dcvm RDP 접속

Add roles and features 클릭 

![](../../../.gitbook/assets/dcvm-dc-install.png)

다음

![](../../../.gitbook/assets/dcvm-dc-install2.png)

다

![](../../../.gitbook/assets/dcvm-dc-install3.png)

다음 

![](../../../.gitbook/assets/dcvm-dc-install4.png)

Server Roles에서 AD서비스와 DNS Server를 선택

![](../../../.gitbook/assets/dcvm-dc-install4_2.png)

DNS Server 선택시 동적IP 경고가 뜬다 일단 다음을 누르고 portal에서 IP를 정적으로 바꿔줌 

![](../../../.gitbook/assets/dcvm-dc-install5.png)

AD 서비스와 DNS Sever 체크 후 다음

![](../../../.gitbook/assets/dcvm-dc-install6.png)

다음 

![](../../../.gitbook/assets/dcvm-dc-install7.png)

다음 

![](../../../.gitbook/assets/dcvm-dc-install8.png)

다음 

![](../../../.gitbook/assets/dcvm-dc-install9.png)

재시작 체크 후 확인, install

![](../../../.gitbook/assets/dcvm-dc-install10%20%281%29.png)

설치되는 동안 리소스 그룹에서 dcvm NIC IP 구성에서 프라이빗 IP 주소를 정적으로 변

![](../../../.gitbook/assets/dcvm-dc-install11.png)

정적으로 변경 후 저장, dcvm, sqlvm 모두 프라이빗 IP를 정적으로 변경\(PIP는 모두 정적으로 생성됨\) 

![](../../../.gitbook/assets/dcvm-dc-install12.png)

