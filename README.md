<div align="center">

<img src="https://raw.githubusercontent.com/so1s/.github/main/static/logo.png" alt="So1s Logo" width="50%" />

# So1s Helm Chart

Kubernetes, Helm 기반 Application 배포 패키지

[프로젝트 소개 페이지로 돌아가기](https://github.com/so1s)

</div>

## 주요 기능

- Batteries included Helm Chart 구성
- Argo CD App of Apps 패턴을 통한 부트스트래핑 지원
- API 서버 / 백오피스 대시보드 구성
- Postgres와 Local Storage Provisioner를 통한 클러스터 내 백엔드 DB 구성
- GPU Operator 구성을 통한 Node GPU Discovery 기능 제공
- Grafana 기반 빌링 대시보드 제공
- Grafana 기반 SLI / SLO 대시보드 제공
- Grafana 기반 API / 인퍼런스 서버 메트릭 대시보드 제공
- Grafana 기반 Kubernetes CPU / Memory / GPU 모니터링 대시보드 제공
- DCGM Exporter를 기반으로 한 Prometheus GPU Metrics 기능 제공
- Promtheus Custom Metrics를 기반으로 한 HPA Rules 구성 기능 제공
- Bin Packing 알고리즘을 통한 Kubernetes Scheduler Plugin 구현, GPU 스케줄링 효율 개선
- External DNS를 통한 Ingress - Route 53 DNS 연동
- Wildcard DNS 기반 Ingress 구성, Istio Service Mesh를 사용한 zero downtime 서브도메인 라우팅 & ELB 비용 절약
  - Use Case) ELB 비용 월 226달러 -> 4달러로 감소
- EFK Stack을 활용한 로깅 수집, 대시보드 지원
- Fluent Bit를 활용한 메모리 효율적인 로깅 메트릭 수집 지원
- Kiali를 통한 실시간 트래픽 Tracing 대시보드 지원
- Sealed Secrets를 활용한 Secret 비대칭 암호화 및 Git 기반 관리 지원

## 사용 기술

- Kubernetes
- Helm
- Argo CD
- Prometheus / Grafana
- External DNS
- Istio / Kiali
- NVIDIA GPU Operator / DCGM Exporter
- Elasticsearch / Fluent Bit / Kibana
- Sealed Secrets


## 사용 방법

이 레포는 Public Template으로 제공됩니다. 보일러플레이트로 사용하시려면 상단의 `Use This Template` 버튼을 눌러주세요!

Template의 형태로 복제하신 뒤, 포크된 레포에서 Sealed-Secrets와 연관된 Secrets를 정의하시고 깃에 반영하셔야 합니다.

### Sealed Secrets 인증서 가져오기 (선택)

클러스터를 여러번 프로비저닝할 경우 현재 클러스터에 있는 Sealed Secrets 인증서를 가져와서 보관한 뒤, 인증서를 다른 클러스터에 적용해야 합니다. 

[Infra 매뉴얼의 해당 인스트럭션](https://github.com/so1s/so1s-infra/blob/main/live/README.md#sealed-secrets-%EC%9D%B8%EC%A6%9D%EC%84%9C-%EB%B3%B4%EA%B4%80--%EC%9E%AC%EC%82%AC%EC%9A%A9)을 참고해주세요!

### Secrets env 파일 작성

로컬 Deploy 루트 디렉토리에 secrets.env 파일을 작성합니다.

Git에는 반영되지 않으니, 안심하셔도 됩니다.

```
# 64바이트 이상의 JWT 암호화용 키
jwt-secret=<>

# S3 접근 권한이 있는 IAM 유저의 access key
aws-s3-access-key=${S3_USER_ACCESS_KEY}
# S3 접근 권한이 있는 IAM 유저의 secret key
aws-s3-secret-key=${S3_USER_SECRET_KEY}
# S3 bucket name
aws-s3-bucket=${S3_BUCKET_NAME}

# S3 버킷의 AWS 리전
aws-region=ap-northeast-2

# DB 엔드포인트. 기본값은 클러스터 Postgres이며, jdbc 포맷을 지킨다면 다른 외부 DB와 연동도 가능
aws-aurora-endpoint=jdbc:postgresql://so1s-database/so1s
# DB 사용자명. 클러스터 Postgres를 사용한다면 임의로 바꿀 수 있음
aws-aurora-username=so1s
# DB 사용자 패스워드. 클러스터 Postgres를 사용한다면 임의로 바꿀 수 있음
aws-aurora-password=${DB_PASSWORD}

# 클러스터 Postgres 데이터베이스명. 백엔드와 연동한다면 DB 엔드포인트 마지막 path와 동일하게
POSTGRES_DB=so1s
# 클러스터 Postgres 사용자명. 백엔드와 연동한다면 aws-aurora-username과 동일하게
POSTGRES_USER=so1s
# 클러스터 Postgres 사용자 패스워드. 백엔드와 연동한다면 aws-aurora-password와 동일하게
POSTGRES_PASSWORD=${DB_PASSWORD}
```


### Sealed Secrets로 변환

```bash
./generate-sealed-secrets.sh
```

파일이 제대로 생성되었으면 커밋하시면 됩니다.
클러스터의 공개 키를 사용해서 암호화되었기 때문에 git 레포에 반영되어도 안전합니다.
