title=TCoverview
date=2017-10-25
type=page
status=published
big=TCoverview
summary=TCOverview
parent=TOASTCloudOverview
nation=ja
~~~~~~



dev버전 입니다.

## TOAST Cloud Overview

TOAST Cloude란 NHN 엔터테인먼트에서 제공하는 클라우드 서비스입니다.

* 누구나 쉽게 개발할 수 있는 서비스를 제공하는 퍼블릭 클라우드
* 개발에만 전녕할 수 있도록 인프라에서 플랫폼까지 다양한 솔루션 제공
* 웹 브라우 상에서 인프라에서 플랫폼까지 모두 관리할 수 있는 콘솔 제공
* 합리적 비용으로 사업화에 기여


## 서비스 구성
인프라, 개발, 테스트, 운영과 기술지원, 사업화에 필요한 다양한 서비스를 제공합니다.

|서비스|설명|
|-------|-----|
|Infrastructure Service|Open Stack 기반 On-Demand 인프라 서비스|
|Contents Service|이미지 저장 및 배포 플랫폼|
|Analystics Service|앱 데이터 분석 운영 플랫폼 지원|
|Game Service|게임 개발 지원|
|Notification Service|모바일 푸쉬 및 sms 플랫|
|Security Service|보안 플랫폼|
|Common Service|개발/테스트/서비스에 필요한 공통 도구|


### Infrastructure Service
클라우드 인프라를 통해 저렴한 비용으로 인프라 리소스를 필요한 만큼 사용할 수 있습니다.

|서비스 정보|설명|
|----------|--------------|
|Compute|-게임 서버, 웹서버, DB서버 등 용도에 따라 적절한 VM 인스턴스 생성.<br>-외장 하드처럼 마운트하여 쓸 수 있는 볼륨 제공<br>-서버 환경 스냅샷을 이미졸 저장하고, 이를 재사용 가능.<br>-가상 사설 네트워크를 구축할 수 있고 외부에서 접속 가능한 공인 IP제공.<br>-대규모 트래픽 분산 처리를 위하여 로드밸런서 지원.|
|Object Storage|-파일 갯수,크기 제약 없이 확장 가능한 고가용성, 안정성을 가진 REST 기반 대용량<br>-스토리지 서비스|
|Monitoring|-별도의 설치 없이 서버 및 네트워크 사용량을 확인할 수 있는 가상 리소스 모니터링<br>-기능 제공.|

### Contents Service
이미지의 저장 및 배포를 제공하는 플랫폼을 사용할 수 있습니다.

|서비스 정보|설명|
|----------|-----|
|Image|-이미지의 저장, 편집, 전송을 제공하는 이미지 플랫폼<br>-실시간 썸네일 생성 및 동적 전송.|
|CDN|-캐시 서버를 이용한 빠른 콘텐츠 배포<br>-네트워크 전송량, 콘텐츠 배포 통계 제공.|
		
### Analytics Service
데이터 분석 및 수집에 필요한 플랫폼을 사용할 수 있습니다.

|서비스 정보|설명|
|----------|-----|
|App Analytics|-대시보드, 실시간 모니터링 지표, 이용자/매출/게임 상세 분석.<br>-커스텀 이벤트 분석 제공, 유입 채널별 이용자 트래킹 및 LTV에 기반을 둔 ROI 제공.<br>-캠페인 목적에 따른 이용자 타겟팅과 캠페인 실행 기능 제공.|
|Log & Crash Search|-앱 오류 로그 수집, 저장, 조회 및 크래쉬 발생 원인 정보 제공 서비스.|

        
### Game Service
게임 개발에 필요한 플랫폼을 사용할 수 있습니다.

|서비스 정보|설명|
|----------|-----|
|Leaderboard|-대규모 트래?에서 안정적으로 동작하는 일간, 주간, 월간, 전체 랭킹 서비스|
|Real Time Multiplayer|-룸기반의 실시간 멀티플레이 게임을 지원하는 네트워크 서비스|


### Notification Service
모바일 푸쉬 및 SMS 플랫폼을 사용할 수 있습니다.

|서비스 정보|설명|
|----------|-----|
|Push|-APNS / CGM / TENCENT 를 통한 푸쉬 제공<br>-메시지 전송 결과 확인 기능 제공.|
|SMS|-SMS / LMS / MMS 발송 기능 제공|


### Security Service
보안을 강화할 수 있는 플랫폼을 사용할 수 있습니다.

|서비스 정보|설명|
|----------|-----|
|AppGuard|-애플리케이션의 코드 조작을 방지.<br>-다양한 조작 툴을 패턴이나 우회가 힘든 행위 기반으로 탐지, 제재|
|Security Check|-보안 취약점 사전 검수<br>-취약점에 대한 대응 방법 가이드|
|CAPTCHA|-문자 / 음성 CAPTCHA 제공|
|OTP|-사용자의 스마트폰으로 사용 가능한 OTP 제공|


### Common Service
개발, 테스트, 서비스 운영에 필요한 공통 플랫폼을 사용할 수 있습니다.

|서비스 정보|설명|
|----------|-----|
|Launching|-클라이언트 런칭 관리 서비스.<br>-클라이언트 구동 시 서버주소, 클라이언트 버전, 공지 사항의 URL,다운로드 URL을 제공.|
|IAP|-하나의 인터페이스로 모든 마켓을 이용할 수 있는 인앱 결제 서비스.|
|Mobile Test|-물리적인 단말을 웹상에서 대여|
|Address Search|-도로명 주소, 지번 주소, 건물명 검색 기능 제공|


