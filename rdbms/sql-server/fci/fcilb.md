---
description: Azure Load Banlancer 생성 및 구성
---

# 8. Load Banlancer 구성

## Azure Portal Load Balancer 배포 

AlwaysOn 가용성 그룹의 Load Balancer 배포와 동일하여 참고 

{% hint style="info" %}
Load Balancer 생성시 Public IP SKU와 맞춰 줌 
{% endhint %}

프론트엔드 : 10.0.1.7 \(FCIINSTANCE\)  
백엔드 : SQLVM-PRIMARY, SQLVM-SECONDARY\(SQLVM\)  
상태프로브 : 59999\(HP59999\)  
부하분산규칙 : FCIRULE

  


  


