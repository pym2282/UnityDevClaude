---
name: unity-qa-tester
description: Unity 게임 플레이테스트 QA 스킬. MCP 툴로 게임을 직접 실행하고 코드를 분석해서 버그, 밸런스 문제, UX 이슈를 찾아낸다. 결과는 unity-game-director 스킬이 바로 처리할 수 있는 레이어별 형식으로 전달한다. 유저가 "QA 해줘", "테스트 돌려봐", "버그 찾아줘", "밸런스 확인해줘", "플레이테스트", "게임 검수해줘" 같은 말을 하면 반드시 이 스킬을 사용한다. Unity 프로젝트에서 코드나 씬을 분석하거나 게임을 실행해서 문제를 찾는 모든 상황에서 트리거된다.
---

# Unity QA Tester

MCP 툴을 이용해 Unity 게임을 직접 플레이테스트하고, 발견한 이슈를 **unity-game-director**가 바로 처리할 수 있는 형식으로 전달하는 스킬이다.

---

## MCP 연결 확인 (시작 전 필수)

스킬 시작 시 항상 아래 순서로 연결을 확인한다.

### 1. coplay MCP 연결 시도
```
list_unity_project_roots → 실행 중인 Unity 인스턴스 확인
set_unity_project_root → 프로젝트 루트 설정
get_unity_editor_state → 에디터 상태 확인
```

실패하면 → **2. mcp-for-unity 시도**

### 2. mcp-for-unity 연결 시도
```
ReadMcpResourceTool(mcpforunity://instances) → 인스턴스 목록 확인
set_active_instance → 인스턴스 연결
manage_scene(get_active) → 씬 상태 확인
```

**"Session not found" 에러가 나는 경우:**
이는 Claude Code 프로세스가 mcp-for-unity 서버보다 먼저 시작되어 구 세션 ID를 갖고 있기 때문이다.
Claude Code를 완전히 종료 후 재시작하면 해결된다. 서버가 먼저 실행 중인지 확인 후 재시작할 것.
해결이 불가능한 상황이면 → **3. 정적 분석 모드로 전환**

실패하면 → **3. 정적 분석 모드로 전환**

### 3. 정적 분석 모드 (MCP 전체 실패 시)
MCP 없이 파일 시스템 직접 접근으로 분석한다.
```
Glob("**/*.cs") → 스크립트 목록
Read → 핵심 스크립트 읽기
Grep → 수치/패턴 검색
```
결과 보고 시 `(정적 분석 — 런타임 미확인)` 명시.

---

## 워크플로우

총 4단계. 순서대로 실행하되, 각 단계에서 발견한 내용은 메모해두고 마지막에 한 번에 정리한다.

---

### 1단계: 코드 & 씬 분석

게임을 실행하기 전에 코드를 읽어서 무엇을 테스트해야 할지 파악한다.

**MCP 연결 시:**
```
list_files → 프로젝트 구조 파악
read_file → 핵심 스크립트 읽기 (PlayerController, GameManager, Enemy 등)
list_code_definition_names → 클래스/메서드 목록 빠르게 스캔
search_files → 밸런스 수치 (damage, speed, hp, score 등) 검색
list_game_objects_in_hierarchy → 씬 구조 파악
get_game_object_info → 주요 오브젝트 컴포넌트 확인
```

**정적 분석 모드:**
```
Glob("Assets/**/*.cs") → 스크립트 목록
Read → 핵심 스크립트 직접 읽기
Grep(pattern, "*.cs") → 수치/패턴 검색
Glob("Assets/**/*.unity") → 씬 파일 목록 확인
```

**파악해야 할 것:**
- 조작 방식 (입력 키, 이동 방식)
- 핵심 게임루프 (승패 조건, 점수, 진행 방식)
- 밸런스 수치 (체력, 데미지, 속도, 쿨타임, 스폰 간격 등)
- 씬 구성 (어떤 오브젝트들이 있고 어떻게 연결되어 있는지)

---

### 2단계: 정적 검사 (실행 전)

**MCP 연결 시:**
```
check_compile_errors → 컴파일 에러 확인
get_unity_logs → 에디터 로그에 기존 에러/경고 있는지 확인
```

**정적 분석 모드:**
```
Grep("Exception|NullReference|throw", "*.cs") → 잠재적 예외 패턴 검색
Grep("TODO|FIXME|HACK", "*.cs") → 미완성 코드 확인
```

컴파일 에러가 있으면 이후 단계를 진행하지 않고 즉시 이슈로 기록한다.

---

### 3단계: 플레이테스트

**MCP 연결 시** — 게임을 실행하고 시나리오별로 테스트한다:

```
play_game → 게임 실행
```

**기본 테스트 시나리오** (1단계에서 파악한 내용 기반으로 조정):

| 시나리오 | 테스트 방법 |
|---------|-----------|
| 게임 시작 정상 동작 | play_game 후 capture_scene_object로 초기 씬 확인 |
| 핵심 메카닉 동작 | execute_script로 플레이어 조작/스폰/이벤트 트리거 |
| 경계값 테스트 | execute_script로 체력 0, 최대값, 음수 등 극단 케이스 |
| 게임오버 조건 | execute_script로 승/패 조건 강제 트리거 |
| 밸런스 체감 | 코드에서 읽은 수치를 실제 플레이 흐름과 대조 |

```
execute_script → 특정 조건 강제 실행 (적 스폰, 체력 감소, 아이템 획득 등)
capture_scene_object → 이상한 점 발견 시 스크린샷
get_unity_logs → 런타임 에러/경고 수집
capture_ui_canvas → UI 렌더링 확인
```

**로그 분류 기준:**
- `Exception`, `Error` → 버그 (코드 레이어 이슈)
- `Warning` → 잠재적 문제 (심각도 판단 후 분류)
- 반복 로그 → 무한루프/로직 오류 의심

```
stop_game → 테스트 완료 후 정지
```

**정적 분석 모드** — 런타임 테스트 대신 코드 로직 분석으로 대체:
- 조건문 경계값 직접 추적 (null 체크 누락, 잘못된 연산 등)
- 코루틴/이벤트 흐름 수동 추적
- 상속 구조 버그 (메서드 은닉, base 호출 누락 등) 확인

---

### 4단계: 결과 전달

발견한 이슈를 **unity-game-director의 레이어 형식**으로 정리해서 출력한다.

이슈가 없는 레이어는 생략한다. 이슈가 전혀 없으면 `✅ 이슈 없음`으로 간단히 표시.

```
[QA → DIRECTOR]
테스트 대상: {씬명 또는 기능명}
테스트 시간: {플레이 시간 또는 테스트 범위}

🔴 [코드] 버그 / 런타임 에러
- {이슈 설명} | 재현: {재현 방법 또는 로그 메시지} | 심각도: 상/중/하

🟡 [기획] 밸런스 / 게임루프 이슈
- {이슈 설명} | 현재값: {코드에서 읽은 수치} | 의심 이유: {근거}

🔵 [디자인] UX / 씬 구성 이슈
- {이슈 설명} | 위치: {씬 또는 오브젝트}

🟠 [아트] 비주얼 이슈
- {이슈 설명}

```

---

## 판단 기준

### 밸런스 이슈로 볼 것
코드 수치를 읽고 **게임 장르/의도**에 비추어 명백히 어색한 경우만 이슈로 올린다.
- 플레이어 이동속도 0이거나 비정상적으로 크거나 작은 경우
- 데미지가 체력의 100% 이상 (즉사 구조인지 의도적인지 불명확할 때)
- 적 스폰 간격이 0 이하이거나 극단적으로 작은 경우
- 쿨타임 없는 무제한 능력 사용

"이게 의도인지 버그인지 모르겠다"면 이슈로 올리되, 의문임을 명시한다.

### 이슈 심각도
- **상**: 게임을 진행할 수 없게 만드는 크래시, 무한루프, 승패 조건 미동작
- **중**: 진행은 되지만 명백히 잘못 동작하는 것 (점수 미집계, 충돌 미동작 등)
- **하**: 시각적 어색함, 경미한 수치 이상, 로그 경고

---

## 참고: execute_script 패턴

테스트용 코드를 실행할 때 쓸 수 있는 기본 패턴들:

```csharp
// 플레이어 체력 강제 설정
var player = GameObject.FindFirstObjectByType<PlayerController>();
if (player != null) player.health = 1;

// 적 강제 스폰
var spawner = GameObject.FindFirstObjectByType<EnemySpawner>();
if (spawner != null) spawner.SpawnEnemy();

// 게임오버 강제 트리거
var gm = GameObject.FindFirstObjectByType<GameManager>();
if (gm != null) gm.GameOver();

// 현재 씬의 주요 수치 읽기
var player = GameObject.FindFirstObjectByType<PlayerController>();
Debug.Log($"[QA] HP:{player?.health} Speed:{player?.moveSpeed}");
```

실제 클래스명/필드명은 1단계에서 읽은 코드에 맞게 조정한다.
