---
description: 수동 Failover 테스트
---

# 8. 수동 Failover

## 쿼리로 수동 Failover 

먼저 각 서버에 접속 확인 SQLVM-PRIMARY 서버에 AG가 \(primary\),   
SQLVM-SECONDARY 서버에 AG가 \(secondary\)로 되어있음 

![](../../../.gitbook/assets/loadbalancer_set19.png)

{% hint style="info" %}
수동 Failover시 Failover 할 대상 서버에 직접 접속 후 쿼리를 실행해야 됨 
{% endhint %}

리스너 접속 또는 PRIMARY 서버에서 수동 Failover 쿼리를 실행 할 경우 오류 발생 

![](../../../.gitbook/assets/loadbalancer_set20.png)

```text
Cannot failover availability group 'AG1' to this instance of SQL Server.  
The local availability replica is already the primary replica of the availability group.  
To failover this availability group to another instance of SQL Server, run the failover command on that instance of SQL Server.  
If local instance of SQL Server is intended to host the primary replica of the availability group, then no action is required.
```

Failover 대상 서버에 접속 후 진행 

![](../../../.gitbook/assets/loadbalancer_set21.png)

서버 새로고침 후 AG1의 가용성 그룹이 secondary에서 primary로 바뀜

![](../../../.gitbook/assets/loadbalancer_set22.png)

## SQLVM-PRIMARY 서버를 중지 시켜 장애상황 발생시 

SQLVM-PRIMARY 중

![](../../../.gitbook/assets/loadbalancer_set23.png)

SQLVM-SECONDARY 서버 접속 후 상태 확인 

![](../../../.gitbook/assets/loadbalancer_set24.png)

{% hint style="info" %}
운영서버 중지 후 모든 가용성 그룹이 SQLVM-SECONDARY 서버로 바뀌고 자동 failover 됨   
10.0.1.5 서버의 가용성 그룹이 모두 \(primary\)로 바뀐걸 볼 수 있다. 오 신기신기 정상 작동함
{% endhint %}

