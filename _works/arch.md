---
layout: works-single
title: "Arch Linux 베이스 하이퍼랜드(Hyprland) 커스텀 데스크톱 환경 구축"
category: "Infra / Enviroment"
category_slug: "environment-setup"
image: assets/img/works/arch.png
short_description: "Arch Linux 최소 설치(CLI)부터 Wayland 기반 Hyprland 타일링 윈도우 매니저 최적화까지, 생산성을 극대화한 맞춤형 개발 환경 구축 프로젝트입니다."

live_preview: "https://github.com/papalooo/dotfiles"
full_image: assets/img/works/arch.png

info:
  - label: "구축 연도"
    value: "2020-2026"

  - label: "사용 운영체제"
    value: "Arch Linux (Kernel: Linux-zen / 오피셜)"

  - label: "핵심 스택"
    value: "Hyprland(Wayland), Fish shell, chezmoi, Pacman"

  - label: "GitHub"
    value: "<a href='https://github.com/papalooo/dotfiles'>저장소 보기</a>"

description1:
  show: yes
  title: "환경 구축 개요 및 아키텍처"
  text1: |
    무거운 데스크톱 환경(GNOME/KDE) 대신 시스템 자원을 최소화하고 작업 효율을 극대화하기 위해, CLI 상태의 바닥(Base)부터 직접 파티셔닝 및 마운트를 진행하여 빌드한 독립적 개발 환경입니다. 단순한 GUI 설치를 넘어 OS 핵심 레이어부터 윈도우 컴포지터까지 제어하도록 설계했습니다.

    **핵심 컴포넌트 구성:**
    - **Base OS:** UEFI 환경 기반 수동 디스크 파티셔닝(GPT), 파일 시스템 포맷 및 파일 시스템 마운트 후 `pacstrap`을 통한 미니멀 아키텍처 설치
    - **Window Manager:** Wayland 기반의 고성능 동적 타일링 컴포지터인 **Hyprland** 채택 및 애니메이션/윈도우 룰 커스텀 셋업
    - **Shell Environment:** 빠른 자동완성 및 직관적인 스크립팅을 지원하는 **Fish shell** 및 프롬프트 최적화
    - **System Utilities:** Waybar(상태 표시줄), Rofi-lbonn(애플리케이션 런처), Dunst/Mako(알림 데몬)를 연동한 경량 데스크톱 에코시스템 구성

description2:
  show: yes
  title: "형상 관리 및 자동화 포인트"
  text1: |
    OS 특성상 빈번하게 발생하는 환경 변화와 다중 기기 간의 설정 동기화 문제를 해결하기 위해 **도트파일(Dotfiles) 선언형 관리**에 집중했습니다. 수동 설정 요소를 제거하고 코드로서의 인프라(IaC) 개념을 개인 워크스페이스에 적용했습니다.

    **주요 성과 및 프로세스:**
    - **chezmoi 도입:** Git 기반 도트파일 관리 도구인 chezmoi를 활용하여 커스텀 단축키 설정, 환경 변수, 애플리케이션 컴포지션 템플릿화 및 암호화 관리
    - **종속성 스크립트화:** 기본 베이스 설치 이후 Hyprland 구동에 필요한 필수 AUR 패키지 및 개발 도구들을 한 번에 배포할 수 있는 프로비저닝 워크플로우 정립
    - **성능 및 보안 최적화:** 시스템 하드웨어 가속(GPU 가속) 설정을 통한 컴포지터 렌더링 오버헤드 최소화 및 불필요한 시스템드(systemd) 서비스 비활성화를 통한 부팅 속도 및 가용 메모리 확보
---