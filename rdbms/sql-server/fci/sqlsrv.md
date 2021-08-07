---
description: FCI 구성을 위한 VM 준비
---

# 3. SQL Server 재설치

## SQL IaaS 확장 제거 

SQL 가상 머신을 배포한 경우 SQL Server 삭제 전 IaaS 확장 등록 취소 

![](../../../.gitbook/assets/vm_setting.png)

## SQL Server 제거 

SQLVM-PRIMARY와 SQLVM-SECONDARY 모두 진행 

![](../../../.gitbook/assets/vm_setting2.png)

## 방화벽 오픈 

1433, 59999, 445 

![](../../../.gitbook/assets/vm_setting3.png)

![](../../../.gitbook/assets/vm_setting4.png)

## Failover Clustering Feature 설치 

설치 과정은 AlwaysOn Group 구성의 Failover Clustering Feature 설치와 동일 

## 도메인 가입 

구성 과정은 AlwaysOn Group 구성의 도메인 가입과 동일 

### 도메인 가입전 Azure Portal NIC DNS 설정 

구성 과정은 AlwaysOn Group 구성의 DNS 설정과 동일 

 





