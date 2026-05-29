---
layout: works-single
title: "LLM 기반 웹 취약점 진단 및 보고서 작성 자동화 애플리케이션"
category: Development
category_slug: development
image: assets/img/works/network-scanner.jpg
short_description: "Python 기반으로 개발한 LLM 을 활영하여 웹 서비스에 대한 취약점을 로컬환경에서 분석한 후 보고서를 작성하는 프로젝트입니다."


live_preview: "https://github.com/honggildong/network-scanner"
full_image: assets/img/works/jangijoim.png

info:
  - label: "제작 연도"
    value: "2026"

  - label: "사용 기술"
    value: "Python, Gemini API, Markdown Parser"

  - label: "유형"
    value: "팀 프로젝트"

  - label: "GitHub"
    value: "<a href='https://github.com/papalooo/jangijoim'>저장소 보기</a>"

description1:
  title: "프로젝트 소개"
  text1: "<p>웹 서비스의 보안 취약점을 자동으로 식별하고, LLM을 연동하여 전문적인 진단 보고서 작성을 자동화하는 로컬 기반 애플리케이션입니다. 단순 스캔 결과 나열에 그치지 않고, 식별된 취약점의 위험도 분석 및 맞춤형 대응 방안을 컨텍스트에 맞게 생성하는 것을 목표로 합니다.</p><p>주요 기능:</p><ul><li>웹 서비스 대상 취약점 데이터 수집 및 분석</li><li>Gemini API 연동을 통한 취약점 발생 원인 및 위험도(Severity) 평가</li><li>보안 방법론 가이드라인 기반의 프롬프트 엔지니어링 적용</li><li>패치 및 대응을 위한 교정(Remediation) 코드 제안 및 마크다운 보고서 자동 생성</li></ul>"

description2:
  title: "개발 과정과 배운 점"
  text1: "<p>스캔 결과 데이터를 LLM이 정확하게 컨텍스트로 수용할 수 있도록 데이터 구조를 전처리하고, 보안 진단의 신뢰도를 높이기 위해 할루시네이션(환각 현상)을 최소화하는 프롬프트 엔지니어링 기법에 집중했습니다. 이를 통해 자동화 스크립트와 대형 언어 모델을 결합한 보안 워크플로우 설계 능력을 배양했습니다.</p><p>현재 버전의 고도화 계획으로, 분석 성능 향상을 위한 진단 모듈의 리팩토링 및 오프라인 환경을 지원하기 위한 로컬 sLLM 연동을 고려하고 있습니다.</p>"
---