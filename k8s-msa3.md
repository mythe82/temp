
# kubernetes 고도화 과제 

## 1. kubernetes 보안 강화
### 목적
- kubernetes 클러스터의 보안 수준을 향상하여 무단 접근 및 설정 오류로 인한 보안 사고 방지

### 주안점
- pod 보안 정책 도입 필요
    opa gatekeeper: 특정 namespace 외 배포 금지, privileged 권한 금지
    pod 보안 컨텍스트(runasnonroot, readonlyrootfilesystem 등) 강제

- rbac 최소 권한 관리 필요
    role/clusterrole과 rolebinding을 서비스 단위로 분리
    vault를 통해 secrets 외부 관리 및 주기적 갱신 자동화

- kubernetes api server 및 etcd 보안 설정 강화 필요
    kube api 직접 호출 방지, api gateway 활용 제시
    api server 접근은 mtls 및 제한된 cidr만 허용

- 컨테이너 이미지 취약점 분석 및 서명 적용 필요(trivy, cosign)

- 노드 보안
  cis benchmarks, 국가클라우드컴퓨팅보안가이드라인 기반 kubelet/etcd 보안 설정


## 2. backup / migration 지원 기능 개발
### 목적
- 클러스터 전체 상태, 데이터, 설정 정보 백업
- namespace 단위로 복구 가능하게 설계

### 주안점
- velero 기반 백업/복구 구성
    csi snapshot 연동, nfs / s3 오브젝트 스토리지 사용
    etcd snapshot 자동화

- 스토리지 snapshot
    자동 스케줄링: cronjob 기반 정책 설정

- 사용자 데이터 운영, 백업, 이관 방안 제안 필요
    실시간 복구 테스트 매뉴얼 포함


## 3. service mesh 기능 기반 유저 서비스 개발
### 목적
- 서비스 간 통신을 제어하고 보안 및 트래픽 관리, 시각화

### 주안점
- istio 기반 서비스 메시 구성
    sidecar 자동 주입 설정

- mtls(양방향 인증) 및 라우팅 정책 기반 마이크로서비스 제어
    virtualservice, destinationrule 기반 구성
    service-to-service 인증서 자동 갱신(citadel)

- observability 도구 연동
    kiali로 서비스 토폴로지 전체 시각화, jaeger로 개별 요청의 흐름/시간 추적 시각화
    prometheus / grafana / kiali / jaeger


## 4. backend api gateway(api catalog) 개발
### 목적
- 유저 api 트래픽을 통합 관리하고 외부/내부 서비스에 대한 접근 제어 제공
- api 문서화 및 사용자별 접근 제한 포함

### 주안점
- kong / istio gateway 구성
    ingress controller로 gateway 포워딩 구성

- 인증/인가
    keycloak과 연동하여 토큰 기반 인증 설정

- openapi 기반 api 문서 자동화
    swagger 연동 및 api catalog ui 구성
    admin api 및 portal 구성(개발자용 ui)

- rate limiting, quota 적용


## 5. 인증 체계 고도화 개발
### 목적
- 사용자 및 서비스에 대한 인증 체계를 강화하여 보안성과 편의성 확보

### 주안점
- sso 연동(oidc, saml2.0, ad 등)
    사내 ldap/ad 연동

- keycloak 도입 및 커스터마이징
    keycloak을 oidc idp로 설정하여 클러스터에 토큰 기반 로그인 구현

- mfa 적용 및 사용자 권한 분리
    관리/업무 콘솔 별도 구성(rbac, ad)
    사용자 그룹/조직별 권한 분리
    oss간 권한 분리 및 관리

- 로그 및 감사 기록 관리
    loki + fluentbit + grafana로 시각화


## 6. 멀티 클러스터 자동 배포/관리 기능 개발
### 목적
- 여러 클러스터를 효율적으로 관리하고 배포 및 운영 자동화

### 주안점
- cluster api / rancher 기반 클러스터 관리
    클러스터 등록/해제 api 설계
    클러스터별 정책 템플릿화

- gitops 기반 배포 자동화(argocd)
    각 클러스터에 argocd agent 배치
    helm chart 통일(values 분리)

- 클러스터 상태 통합 모니터링
    클러스터 등록 → health check → sync 자동화

## deploy k8s
- https://mcmp.cloudz.co.kr/blog/detail/212
