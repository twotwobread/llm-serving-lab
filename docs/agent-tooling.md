# 에이전트 도구 설정

## 범위

OpenWiki는 프로젝트 위키 관리에 사용하는 기본 도구다. Superpowers는 Codex와 pi 모두 선택 사항이다. 선택 도구가 없어도 프로젝트 작업 절차를 수행할 수 있어야 한다. 사용자 전역 설정과 이미 설치된 전역 스킬은 이 설정으로 제거하지 않는다.

## 실행 환경

`mise.toml`은 Node 22.23.3을 지정한다. OpenWiki 0.7.2의 Node 요구사항은 22.22.0 이상이다. `mise install` 후 저장소 루트에서 `mise exec -- codex` 또는 `mise exec -- pi`로 시작한다. 설정 반영을 위해 새 세션을 사용한다.

## Codex

- `.agents/plugins/marketplace.json`: OpenWiki 기본 제공, Superpowers 선택 가능 목록.
- `.codex/config.toml`: OpenWiki 활성화, Superpowers 기본 비활성화.
- OpenWiki는 upstream 스킬과 MCP를 로컬 플러그인으로 포장했다. 자체 문서 생성 로직은 없다.
- MCP 실행은 mise와 npx를 사용한다. 최초 실행에는 지정 npm 패키지 다운로드가 필요하다.
- CLI에서 최초 등록·설치: 저장소 루트에서 `codex plugin marketplace add .` 후 `codex plugin add openwiki@llm-serving-lab`을 실행한다. 이 명령은 사용자 Codex의 marketplace 등록과 캐시를 갱신한다.
- 신뢰한 프로젝트에서 설정을 로드하고 플러그인 목록을 확인한다. JSON 선언만으로 모든 클라이언트의 설치·활성화를 보장하지 않는다.
- Superpowers를 사용하려면 marketplace에서 설치하고 해당 프로젝트의 `enabled`를 `true`로 변경한다.

## pi

- `.pi/settings.json`: 공식 `npm:openwiki@0.7.2`만 등록한다.
- `.pi/settings.with-superpowers.example.json`: 선택 구성을 보여주는 예시이며 자동 로드되는 설정이 아니다.
- 선택 설치: `mise exec -- pi install --local git:github.com/obra/superpowers@b739a8af570e10a95898f2bdf6cec97467fa6c12`
- 기본 패키지 설치: `mise exec -- pi install --local npm:openwiki@0.7.2`. 등록 확인: `pi list --approve`.
- pi의 공식 프로젝트 신뢰·패키지 설치 절차를 따른다. 예시 파일로 기존 설정을 무조건 덮어쓰지 않는다.

## 참고

- https://github.com/langchain-ai/openwiki
- https://github.com/obra/superpowers
- https://developers.openai.com/plugins/build/plugins

위키 생성은 아직 수행하지 않았다. 설치·로딩 검증과 실제 위키 생성 검증은 구분한다.
