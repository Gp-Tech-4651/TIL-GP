### Context
n8n을 활용해 디스코드(Discord) 메시지를 트리거로 삼아, 수신된 데이터를 LLM 에이전트가 가공하고, 이를 GitHub에 자동으로 기록(Push)하는 워크플로우를 구축했습니다. 최종적으로 README.md에 새로운 TIL 인덱스를 추가하여 문서화 과정을 완전 자동화했습니다.

### Core
n8n 워크플로우의 주요 노드 구성은 다음과 같은 논리 흐름을 가집니다.

* **Discord Trigger**: 디스코드 특정 채널의 메시지를 감지하여 JSON 형태의 데이터를 추출합니다.
* **AI Agent**: 수신된 텍스트를 TIL(Today I Learned) 형식으로 정제하고 마크다운 문법에 맞춰 변환하는 프롬프트를 수행합니다.
* **GitHub Node**: `Upsert File` 또는 `Create File` 기능을 사용하여 지정된 레포지토리에 커밋하고 푸시합니다.
* **README Update**: 파일 추가 이후 README.md의 인덱스 섹션을 찾아 새로운 TIL 링크를 추가하는 파이썬(Python) 코드나 노드 기능을 실행합니다.

```json
{
  "workflow_logic": "Discord(Trigger) -> AI Agent(Parsing) -> GitHub(Push) -> GitHub(Update README)"
}
```

### Insight
n8n 워크플로우 설계 시 가장 중요한 핵심 원칙은 '데이터 흐름의 명확한 파악'입니다. 각 노드가 어떤 입력을 필요로 하고(Input Schema), 처리 후 어떤 출력을 반환하는지(Output Schema)를 이해한다면 복잡한 자동화도 모듈 단위로 분해하여 쉽게 구축할 수 있습니다. 특히 에이전트와 버전 관리 시스템을 연결하는 과정에서 데이터 구조의 정규화가 선행되어야 오류 없는 자동화가 가능합니다. 

**출처:** [n8n Workflow Nodes Documentation](https://docs.n8n.io/nodes/)