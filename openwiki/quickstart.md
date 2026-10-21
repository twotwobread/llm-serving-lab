---
type: Guide
title: 프로젝트 탐색 안내
description: LLM 서빙 안정성 실험의 설계 상태와 질문별 최소 탐색 경로를 안내한다.
tags: [navigation, serving, project-status]
verified:
  - by: openwiki/0.7.2
    at: 2026-10-21T09:25:17.281Z
sources:
  - id: openwiki-source-8e7150eb410f39fb723ea62d
    resource: repo://.agents/plugins/marketplace.json
  - id: openwiki-source-117135992e856c79760703a3
    resource: repo://.harness/documentation.md
  - id: openwiki-source-103eb544c4f3ef978d181f63
    resource: repo://.pi/settings.json
  - id: openwiki-source-8037e2358a2c4f9b2c722a11
    resource: repo://AGENTS.md
  - id: openwiki-source-9d3ff5e157e729d55d439c78
    resource: repo://docs/project-status.md
generated: { by: "codex", at: "2026-10-21T09:25:17.281Z" }
---

# 프로젝트 탐색 안내

LLM Serving Lab은 Kubernetes에서 추론 서버의 종료·교체 안정성을 재현하고 검증하기 위한 프로젝트다. 현재는 초기 설계와 에이전트 도구 설정 단계다. 제품 Operator·CRD·faker·GPU 실험은 아직 구현되지 않았다.

이 위키에서 검증한 범위는 **사용자와 합의한 설계 방향 및 저장소에 존재하는 설정**이다. 문서의 검증 메타데이터는 제품 동작이나 GPU 실험 성공을 의미하지 않는다.

## 질문별로 읽기

| 알고 싶은 내용 | 읽을 페이지 |
| --- | --- |
| 왜 서빙과 실험을 별도 CR로 나누는가? 어떤 리소스를 소유하는가? | [서빙과 실험의 책임 분리](architecture/serving-experiments.md) |
| GPU 없이 무엇을 확인하고 실제 엔진에서는 무엇을 검증하는가? | [로컬과 실제 GPU 검증](testing/validation-environments.md) |
| Codex·pi에 어떤 패키지가 설정되어 있는가? | [OpenWiki 연동](integrations/agent-tooling.md) |

## 작업 규칙과 프로젝트 지식

작업 절차는 [하네스의 개발 흐름](../.harness/workflow.md), 문서의 역할·신뢰 경계는 [문서 관리 규칙](../.harness/documentation.md)이 기준이다. 이 페이지는 그 지침을 복제하지 않는다.

위키는 필요한 페이지와 섹션만 선택해서 읽는 프로젝트 맥락이다. 원자료 문서의 존재만으로 해당 내용을 검증된 사실로 취급하지 않는다. 중요한 동작은 현재 코드와 테스트를 대조해야 하며, 없는 구현·검증 결과는 추정하지 않는다.

## 현재 작업의 출발점

첫 문제는 스트리밍 중 서버 축소·교체에서 요청 중단을 재현하고 원인을 분석하는 것이다. 다음 설계에서는 CR API와 상태, 종료 시나리오 조율, 검증 기준을 구체화해야 한다. 초기 방향을 합의했다는 것과 상세 설계 리뷰를 마쳤다는 것은 구분한다.

실험 실행이나 서빙 배포용 명령은 아직 제공하지 않는다. 에이전트 도구 설정을 제품 실행 환경이 완성된 것으로 해석하지 않는다.
