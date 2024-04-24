목차

## MTU
MTU(Maximum Transmission Unit)은 장치가 수용할 수 있는 데이터 패킷의 최대 크기.  
이 크기를 기준으로 데이터가 패킷화되며 전송되는 회선 상의 모든 장치의 MTU보다 패킷이 더 크면 분할될 수 있다.

---
## 패킷이 분할되지 않는 경우
### IPv6
분할 절대 불가

### IPv4
IPv4 헤더의 flags 필드에 bit가 1이 되면 "Don't Fragment" 플래그가 활성화.

---
## MSS
MSS(Maximum Segment Size)는 오로지 세그먼트의 크기(payload)만 나타낸다.