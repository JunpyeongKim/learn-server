27.리눅스 컨테이너로 인프라를 순식간에 구축하기
=====================================
- Docker 를 사용하면 Linux Container 를 사용한 경량 가상화 환경을 간단하게 프로그래밍 방식으로 관리하고 활용 가능 

27.1 도커란
- 서버 가상화
    - 완전 가상화
        - 하드웨어 레벨까지 통째로 소프트웨어로 에뮬레이션하는 가상화
        - 즉, Hypervisor --> VM --> OS --> Middleware
        - 하드웨어 에뮬레이션 & OS 부팅 시간 필
- Container 형 가상화와 LXC
    - 하드웨어 레벨의 가상화는 하지 않는다
    - 커널의 기능으로 여러 사용자 공간을 만들어 사용자 프로세스에서 보이는 자원을 나주거나 분리해 가상 환경 제공
    - ex) Platform as a Service - Heroku, Test as a Service - Travis CI
- Container 형 가상화의 장점
    - 경량 가상 환경
    - LXC(Linux Container)는 리눅스 커널의 기능을 사용해 구현된다
- Docker
    - (*) LXC 는 진입 장벽이 높은 경우도 존재
    - Docker = LXC + AUFS(Another UnionFS) --> Middleware
    
27.2 도커의 기동

27.3 도커 첫걸음

27.4 리눅스 컨테이너 속으로

27.5 실행 중인 컨테이너의 상태 확인

27.6 도커 이미지

27.7 Dockerfile

27.8 도커의 리모트 API

27.9 도커의 사용 예

27.10 Immutable Infrastructure
