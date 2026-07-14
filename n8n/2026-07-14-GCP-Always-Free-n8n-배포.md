### Context
n8n을 로컬 환경이 아닌 24시간 가동되는 클라우드 환경에서 운영하고자 할 때 발생하는 비용 부담을 해결하기 위해 GCP(Google Cloud Platform)의 Always Free 프리티어 인스턴스(e2-micro)를 활용한 구축 방법을 정리한다. 특히 별도의 고정 IP나 복잡한 방화벽 설정 없이 외부 웹훅(Webhook)을 연결하는 효율적인 네트워크 구성 방안을 다룬다.

### Core
Docker 및 Ngrok을 활용한 컨테이너 기반 배포 구성 예시이다.

* Docker Compose 설정 (docker-compose.yml)
```yaml
version: '3'
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    ports:
      - "5678:5678"
    volumes:
      - ./n8n_data:/home/node/.n8n
    restart: always

  ngrok:
    image: ngrok/ngrok:latest
    command: http n8n:5678
    environment:
      - NGROK_AUTHTOKEN=당신의_NGROK_토큰
```

* 터널링 실행
```bash
docker-compose up -d
```

### Insight
GCP e2-micro 인스턴스는 메모리 제한(약 1GB)이 있으므로 n8n의 복잡한 워크플로우 실행 시 부하가 발생할 수 있다. Ngrok은 임시 개발 환경에는 적합하지만, 프로덕션 환경에서는 SSL 인증서 관리와 안정성을 위해 Nginx Reverse Proxy와 도메인을 연결하는 것을 권장한다. 또한 Always Free 정책은 특정 리전(예: us-west1 등)에 한정되므로 반드시 Google Cloud 공식 문서를 통해 가용 리전을 확인해야 한다.

**출처:** [n8n 공식 문서 - Docker 설치 가이드](https://docs.n8n.io/hosting/installation/docker/), [Google Cloud Always Free 정책](https://cloud.google.com/free/docs/free-cloud-features#compute)