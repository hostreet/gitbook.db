# 7. SQL Server FCI 설치

## SQLVM-PRIMARY에 SQL Server Failover Cluster 새로 설치 

New SQL Server failover cluster installation 클릭 

![](../../../.gitbook/assets/vm_setting11.png)

네트워크 이름과 인스턴스명 입력 

![](../../../.gitbook/assets/vm_setting22.png)

클러스터 리소스 그룹에 관한 내용인데 다음으로 넘어감 

![](../../../.gitbook/assets/vm_setting13.png)

클러스터에 추가한 공유 디스크 선택 

![](../../../.gitbook/assets/vm_setting23.png)

DHCP 체크를 해제하고 static IP로 지정 10.0.1.7 

![](../../../.gitbook/assets/vm_setting15.png)

SQL Server 계정 설정 

![](../../../.gitbook/assets/vm_setting24.png)

SQL Server 데이터 디렉토리 지정 

![](../../../.gitbook/assets/vm_setting25.png)

{% hint style="info" %}
SQL Server 설치 전 클러스터\(WSFC\)에 추가한 공유 디스크\(FCIDisk\) G 드라이브 
{% endhint %}

{% hint style="info" %}
테스트에서는 공유 디스크를 1개만 추가하여 Data, Log, Backup 한 드라이브에 지정 했으나  
실제 프로젝트 환경에서는 Data, Log, Backup 스토리지를 분리하여 진행함   
\(공유 디스크만 추가하면 됨\) 
{% endhint %}

![](../../../.gitbook/assets/vm_setting26.png)

Install 

![](../../../.gitbook/assets/vm_setting27.png)



