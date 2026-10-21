---
type: Testing
title: 로컬과 실제 GPU 검증
description: faker로 검증할 실험 도구와 실제 추론 엔진의 검증을 분리하는 환경 방향 및 관측 설계를 설명한다.
tags: [testing, gpu, faker, observability]
verified:
  - by: openwiki/0.7.2
    at: 2026-10-21T09:25:17.281Z
sources:
  - id: openwiki-source-70991026f1538e8215a8789e
    resource: repo://docs/development.md
  - id: openwiki-source-b33845b6d447c1af4bc37f86
    resource: repo://docs/superpowers/specs/2026-10-21-serving-reliability-design.md
generated: { by: "codex", at: "2026-10-21T09:25:17.281Z" }
---

# 로컬과 실제 GPU 검증

## 현재 확인 범위

이 페이지는 세션에서 합의한 환경 방향과 관측 설계를 저장소 기록과 대조했다. 아직 클러스터 구축, faker, GPU 실험, 환경 재구축 스크립트는 구현되지 않았다. 아래 내용은 실행 결과나 즉시 사용할 수 있는 설치 절차가 아니다.

## 두 독립 환경

| 환경 | 검증하려는 대상 | 결과의 한계 |
| --- | --- | --- |
| 로컬 Kubernetes + faker | 배포·상태 제어 및 실험·판정 도구 | 실제 모델 로딩과 GPU 동작의 증거가 아님 |
| GPU VM의 독립 Kubernetes | 실제 추론 엔진의 모델 로딩·종료·GPU 동작 | 사용한 모델·GPU·엔진·설정 범위에서만 해석 |

기본 개발은 faker로 수행하고 실제 엔진 검증이 필요한 시점에만 GPU VM을 사용하는 방향이다. 로컬 제어부에 원격 GPU 워커를 조인하는 구조는 초기 범위에 포함하지 않는다. 같은 Operator에 환경별 설정을 적용할 계획이며 실제 호환성은 아직 검증되지 않았다.

GPU VM의 초기화는 GPU 실행 환경, Kubernetes, device plugin, Operator와 관측 구성까지 자동화할 방향이다. 결과는 VM 외부에 보관한 뒤 임시 자원을 정리한다. TensorDock는 검토한 후보이며 공급자·가격·설치 가능 구성을 확정한 상태가 아니다.

## faker와 실제 엔진의 차이

faker는 로딩 지연, 긴 스트리밍, 정상 종료와 비정상 종료를 의도적으로 만들어 실험이 요청 중단을 탐지하는지 확인하기 위한 도구다. faker에서 만든 실패가 실제 엔진에도 존재한다고 결론 내릴 수 없다. 실제 GPU 검증은 엔진이 같은 조건에서 어떻게 동작하는지 별도로 확인한다.

## 관측의 연결

실험 결과는 단일 성공·실패 값보다 요청이 언제, 어느 서버에서, 어떤 종료 과정과 함께 중단됐는지 설명할 수 있어야 한다. 예정된 관측 정보는 다음과 같다.

| 관측 위치 | 기록할 정보 |
| --- | --- |
| 부하 클라이언트 | 요청 ID, 시작·첫 토큰 시각, 정상 완료 여부, 연결 오류 |
| 추론 서버 | 요청 처리 시작·완료, 종료 신호 수신, 남은 요청 |
| Kubernetes | Pod 삭제, readiness·EndpointSlice 변경, 컨테이너 종료 시각·이유 |

요청 ID·Pod UID·실험 ID·시각을 연결하는 방향이다. 요청 ID는 Prometheus 라벨로 사용하지 않고 로그 또는 트레이스에 기록하도록 설계했다. 공용 지표·로그 수집 시스템을 재사용하며 실험마다 관측 스택 전체를 생성하지 않는다.

원본 데이터는 CR status 외부에 저장하고 CR에는 요약·판정·결과 위치만 남길 예정이다. 비교를 위해 이미지 버전, 모델, GPU 종류, 서버 설정과 부하 조건도 결과에 포함한다.

## 아직 결정되지 않은 부분

추론 엔진, Kubernetes 배포판, 부하 도구, 관측·저장 제품, API 스키마, 성공 임계값과 재현 가능한 실행 명령은 아직 확정되지 않았다. 현재 문서는 GPU 테스트 통과나 개선 수치를 주장하지 않는다.

서버와 실험의 책임은 [설계](../architecture/serving-experiments.md), 문서 작성 도구는 [연동 구성](../integrations/agent-tooling.md), 전체 탐색은 [시작 안내](../quickstart.md)를 참조한다.
