---
description: Azure 스토리지 계정으로 Cloud witness 구성
---

# 5. Quorum 구성

## Azure Storage Account 생성 

Azure Portal에서 아래와 같이 설정 후 나머지 옵션은 디폴트로 생성

![](../../../.gitbook/assets/storageaccnt%20%281%29.png)

다시 SQLVM-PRIMARY 서버 접속 후 쿼럼 세팅 클

![](../../../.gitbook/assets/qrmset.png)

ㄴㄴ

![](../../../.gitbook/assets/qrmset3.png)

ㅇㅇ

![](../../../.gitbook/assets/qrmset4.png)

ㅌㅊ

![](../../../.gitbook/assets/qrmset5.png)

ㅌㅌ

![](../../../.gitbook/assets/qrmset6.png)

스토리지 계정과 공유 액세스 키 입력

![](../../../.gitbook/assets/qrmset8.png)

위 상황에서 다음으로 진행시 TLS1.2에 대한 오류가 발생함..   
방법은 스토리지 계정의 TLS 최소 버전을 1.0으로 내리던지,   
아래 TLS 1.2를 클라이언트 어플리케이션단에서 사용하도록 세팅인데 아래의 클라이언트의 세팅은   
TLS 1.2 사용을 추가만 하는것인지? 아니면 TLS 1.2로만 사용을 하는지 좀 더 알아봐야 함..

여기서는 스토리지 계정의 최소 TLS 버전을 1.0로 낮추고 진행  


관련 내용 링크 :  
[https://docs.microsoft.com/ko-kr/azure/storage/common/transport-layer-security-configure-client-version?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=powershell](https://docs.microsoft.com/ko-kr/azure/storage/common/transport-layer-security-configure-client-version?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=powershell)

```text
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
```





