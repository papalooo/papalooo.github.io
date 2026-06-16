---
layout: post
title: "AI 시대의 모의해킹: LLM을 활용한 공격 표면 분석과 자동화 시나리오"
description: "AI 가 사이버보안에 어떤식으로 작용할 수 있을지에 대한 기술입니다."
date: 2026-06-12 12:00:00 +0900
image: assets/img/works/ai.png
categories: [security, redteam]
tags: [pentest, redteam, llm, automation, dast, sast]
---

## 들어가며

전통적인 모의해킹(Penetration Testing) 업무는 정찰(Reconnaissance) → 취약점 분석 → 익스플로잇 → 권한 상승 → 후속 조치(Post-Exploitation)로 이어지는 정형화된 프로세스를 따른다. 그러나 공격 대상의 규모가 커지고 클라우드, 마이크로서비스, API 중심 아키텍처가 보편화되면서, 수동 분석만으로는 한계가 명확해지고 있다. 이번 글에서는 LLM(Large Language Model)을 모의해킹 워크플로우에 통합하는 방법과, 이를 통해 레드팀 업무가 어떻게 변화할 수 있는지 다룬다.

## 1. 정찰 단계의 자동화: 정보 수집과 컨텍스트화

기존 정찰 도구(Nmap, Amass, Subfinder 등)는 방대한 raw 데이터를 생성하지만, 이를 의미 있는 공격 시나리오로 연결하는 작업은 여전히 분석가의 직관에 의존한다. LLM은 다음과 같은 역할을 수행할 수 있다.

- 포트 스캔 결과와 서비스 배너 정보를 입력받아 잠재적 공격 경로를 자연어로 요약
- 서브도메인 목록에서 명명 규칙을 분석해 내부 시스템 추정(예: `staging-api`, `internal-vpn` 등)
- OSINT로 수집한 기술 스택 정보를 기반으로 알려진 CVE와 매핑

```python
# 예시: 스캔 결과를 LLM에게 컨텍스트로 전달하는 파이프라인
def build_recon_summary(scan_results: dict) -> str:
    prompt = f"""
    다음은 대상 도메인의 Nmap/서브도메인 스캔 결과입니다.
    공격자 관점에서 우선적으로 점검해야 할 엔드포인트와 그 이유를 정리해주세요.

    스캔 결과:
    {scan_results}
    """
    return query_llm(prompt)
```

여기서 핵심은 LLM이 "취약점을 찾아주는 것"이 아니라, **분석가가 우선순위를 정하는 시간을 줄여주는 것**이다.

## 2. SAST/DAST 결과의 통합 스키마화

레드팀과 시큐어 코딩 검토를 병행할 때, Semgrep(SAST)과 Nuclei(DAST)의 결과 포맷이 서로 달라 통합 분석이 어렵다. 이를 해결하기 위해 공통 취약점 스키마를 정의하고, 각 도구의 출력을 정규화하는 작업이 필요하다.

```json
{
  "finding_id": "uuid",
  "source": "semgrep | nuclei",
  "type": "sqli | xss | ssrf | idor | ...",
  "severity": "critical | high | medium | low",
  "location": {
    "file_or_url": "string",
    "line_or_param": "string"
  },
  "evidence": "string",
  "cwe": "CWE-XXX"
}
```

이렇게 정규화된 데이터는 이후 LLM 기반 멀티 에이전트 분석의 입력으로 사용될 수 있다. 예를 들어 SAST에서 발견된 SQL Injection 가능 코드와 DAST에서 실제로 트리거된 동일 엔드포인트의 응답을 교차 검증하면, 오탐(False Positive)을 크게 줄일 수 있다.

## 3. Tree-sitter 기반 AST 분석으로 컨텍스트 보강

Semgrep의 패턴 매칭만으로는 "이 변수가 실제로 사용자 입력에서 유래했는가"와 같은 데이터 흐름 추적이 제한적이다. Tree-sitter를 활용해 AST(Abstract Syntax Tree)를 파싱하면, 함수 호출 체인과 변수 정의-사용 관계를 추출할 수 있다.

```python
import tree_sitter_languages as tsl

parser = tsl.get_parser("python")
tree = parser.parse(source_code.encode())

# 함수 정의 노드를 순회하며 사용자 입력 진입점 추적
def find_taint_sources(node):
    if node.type == "call" and "request" in node.text.decode():
        return node
    for child in node.children:
        result = find_taint_sources(child)
        if result:
            return result
    return None
```

이렇게 추출한 컨텍스트(호출 체인, 진입점, 데이터 흐름)를 LLM에게 함께 제공하면, 단순 패턴 매칭보다 훨씬 정확한 취약점 설명과 우선순위 판단이 가능해진다.

## 4. 멀티 에이전트 구조의 실전 적용

단일 LLM 호출보다 역할을 분리한 멀티 에이전트 구조가 모의해킹 분석 자동화에 더 적합하다. 예를 들어:

1. **Triage Agent**: SAST/DAST 원시 결과를 받아 중복 제거 및 1차 분류
2. **Context Agent**: AST 분석 결과를 바탕으로 코드 흐름 설명 생성
3. **Exploit Reasoning Agent**: 실제 공격 가능성과 PoC 시나리오 작성
4. **Report Agent**: 최종 결과를 보고서 형식으로 정리

이 구조의 장점은 각 에이전트가 좁은 범위의 작업에 집중하기 때문에 환각(Hallucination) 가능성이 줄어들고, 중간 결과를 검증 가능한 단위로 나눌 수 있다는 점이다.

## 5. 한계와 윤리적 고려사항

LLM 기반 자동화는 분석가의 업무를 보조하는 도구일 뿐, 최종 판단과 책임은 여전히 인간 분석가에게 있다. 특히 다음 사항을 주의해야 한다.

- **오탐/미탐의 책임 소재**: 자동화된 도구가 누락한 취약점에 대한 책임은 명확히 정의되어야 한다
- **민감 데이터 처리**: 코드베이스나 스캔 결과를 외부 LLM API로 전송할 경우, 민감 정보 마스킹이 필수다
- **법적/계약상 범위**: 모의해킹은 항상 명시적 승인(Scope of Work) 내에서 수행되어야 하며, 자동화 도구도 이 범위를 벗어나지 않도록 설계해야 한다

## 마무리

모의해킹 업무에 LLM을 통합하는 것은 단순히 "AI가 해킹을 대신한다"는 의미가 아니다. 오히려 반복적이고 시간이 많이 드는 분석 작업을 줄여, 분석가가 더 창의적이고 깊은 수준의 공격 시나리오 설계에 집중할 수 있도록 돕는 것에 가깝다. 앞으로 SAST, DAST, AST 분석을 통합한 파이프라인과 멀티 에이전트 구조는 레드팀 도구체인의 표준이 될 가능성이 높다.