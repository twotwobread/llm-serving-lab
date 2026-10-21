---
type: Integration
title: Codex와 pi의 OpenWiki 연동
description: 저장소 설정에서 확인한 OpenWiki의 두 에이전트 연동 경로와 선택 의존성, 실행 전제를 설명한다.
tags: [openwiki, codex, pi, tooling]
verified:
  - by: openwiki/0.7.2
    at: 2026-10-21T09:25:17.281Z
sources:
  - id: openwiki-source-8e7150eb410f39fb723ea62d
    resource: repo://.agents/plugins/marketplace.json
  - id: openwiki-source-ddb1ca7678050009fa13c959
    resource: repo://.agents/plugins/openwiki/mcp.json
  - id: openwiki-source-4b4aa1a4a3a4bda2cd6a7f44
    resource: repo://.agents/plugins/openwiki/PROVENANCE.md
  - id: openwiki-source-99d5eda80a7d9b8747aeef28
    resource: repo://.agents/plugins/openwiki/skills/openwiki/SKILL.md
  - id: openwiki-source-ab585d88aca3958f2aa6a541
    resource: repo://.codex/config.toml
  - id: openwiki-source-103eb544c4f3ef978d181f63
    resource: repo://.pi/settings.json
  - id: openwiki-source-5499908bad86dcead7be658e
    resource: repo://.pi/settings.with-superpowers.example.json
  - id: openwiki-source-72b5d686f860ea86c8592080
    resource: repo://mise.toml
generated: { by: "codex", at: "2026-10-21T09:25:17.281Z" }
---

# Codex와 pi의 OpenWiki 연동

## 구성 확인 범위

이 페이지는 실제 marketplace, 플러그인, MCP, pi, mise 설정을 확인한 결과다. 설정 파일의 존재와 기본값을 설명하며 모든 클라이언트에서 설치·신뢰·활성화가 완료됐다는 보장은 아니다. 실제 에이전트 세션에서 도구가 노출되는지는 별도로 확인해야 한다.

## 공통 구현과 역할

두 연동은 OpenWiki 0.7.2를 사용하도록 설정돼 있다. 문서 조사·작성 절차는 upstream 스킬을 사용하고, 작업 상태·근거 등록·완료 처리는 OpenWiki 도구에 맡긴다. 저장소의 Codex 포장은 자체 위키 생성 로직을 추가하지 않는다.

스킬이 보이는 것과 MCP 도구가 연결된 것은 별개다. 페이지를 수동 저장한 것만으로 OpenWiki의 생성 절차가 완료된 것으로 간주할 수 없다. 생성 스킬은 begin → plan → 페이지별 작성·제출 → finish 순서를 요구한다.

## Codex 연동 경로

프로젝트 marketplace는 로컬 OpenWiki 플러그인을 기본 항목으로 제공한다. `.codex/config.toml`은 `openwiki@llm-serving-lab`을 활성화하도록 설정한다. 플러그인은 upstream 스킬 사본과 MCP 실행 설정을 묶는다.

MCP 실행 명령은 다음과 같다.

```sh
mise exec node@22.23.3 -- npx --yes openwiki@0.7.2 mcp --host codex
```

따라서 mise와 지정 Node 실행 환경이 필요하고, npm 패키지가 캐시에 없으면 다운로드가 필요하다. 공식 통신 인터페이스가 연결돼야 생성 작업과 검색 도구를 호출할 수 있다. 현 세션에서 도구가 직접 노출되지 않으면 설치 여부와 MCP 연결을 구분하여 진단해야 한다.

## pi 연동 경로

`.pi/settings.json`에는 공식 `npm:openwiki@0.7.2`가 선언돼 있다. pi는 이 프로젝트 패키지를 통해 upstream 연동을 사용한다. 기본 설정 파일에 임의의 optional 필드는 넣지 않는다.

별도 `settings.with-superpowers.example.json`은 선택 패키지를 추가한 구성 예시이며 자동 적용되는 설정이 아니다. 사용자 전역 패키지나 스킬은 프로젝트 파일에서 제거하지 않는다.

## 선택 도구와 버전

Superpowers는 Codex marketplace에서 `AVAILABLE` 항목이고 프로젝트 설정에서는 비활성화돼 있다. pi 기본 패키지 목록에는 없으며 선택 예시에서만 고정 Git 커밋으로 지정돼 있다. 이 선택은 프로젝트의 개발 절차를 특정 작성 도구에 종속시키지 않기 위한 구성이다.

`mise.toml`은 Node 22.23.3을 지정한다. OpenWiki CLI 버전과 동봉된 스킬의 원본 커밋은 플러그인 provenance에 함께 기록돼 있다. 패키지 업데이트 때 두 구성의 호환성을 함께 확인할 필요가 있다.

## 탐색

개발 절차는 [하네스](../../.harness/workflow.md), 문서 신뢰 경계는 [문서 관리 규칙](../../.harness/documentation.md)에 유지한다. 제품 설계는 [서빙과 실험](../architecture/serving-experiments.md), faker·GPU의 검증 경계는 [검증 환경](../testing/validation-environments.md), 전체 경로는 [시작 안내](../quickstart.md)를 참조한다.
