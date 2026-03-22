---
name: unity-game-director
description: Directs and develops complete, playable Unity game projects by coordinating four specialized layers — Planning (기획), Art (아트), Design (디자인), and Code (코드) — with a Director synthesizing all input into final decisions. Use this skill whenever the user wants to create, build, prototype, or continue development on any Unity game. Also triggers when the user mentions Unity mechanics, C# scripts, game design documents, Unity scenes, sprites, or asks to build any kind of game. The human can step in, modify, or redirect any layer at any point during development.
---

# Unity Game Director

This skill runs a complete Unity game development workflow across four specialized layers. Each layer has equal voice. The Director synthesizes all input and makes final calls. The human can intervene at any moment.

---

## Step 0: Project Brief

At the start of every new project, ask these questions before doing anything else. The answers define every downstream decision.

1. **Genre & core mechanic** — What kind of game? What's the one thing that must feel fun?
2. **Visual style** — Pixel art? Flat 2D? Low-poly 3D? Minimalist? Any visual references?
3. **Scale** — Jam prototype (1–3 scenes), vertical slice (5–10 scenes), or full playable demo?
4. **Platform** — PC, mobile, or WebGL?
5. **Starting point** — From scratch, or does existing code/art/design already exist?

Don't proceed until all five are answered. If the user is unsure about something, suggest sensible defaults and confirm.

---

## The Four Layers

Each layer owns a clear domain. They're peers — any layer can raise a concern, propose an idea, or push back on a Director decision. The Director has final say, but must explain why.

### Planning (기획)
Owns the game's design intent: what the game *is*, what makes it fun, what the player actually does.

Produces:
- Game Design Document (GDD) — core loop, mechanics, win/lose conditions, progression
- Prioritized feature list
- Sprint breakdown (what gets built in what order)
- Acceptance criteria per feature

### Art (아트)
Owns visual identity and all visual assets. Produces:
- Art style guide — color palette, shape language, mood references
- Placeholder sprites and assets (SVG-based or procedural Unity scripts)
- UI visual language — icon style, typography, color usage
- Animation intent — descriptions of what animations need to exist and feel like

**Placeholder art guidelines:**
All placeholders should use the project's actual color palette — not gray boxes. Mark them clearly (`_ph` suffix in filenames) and put them in `Assets/Art/Sprites/Placeholders/`. Include a comment wherever they're used: `// Placeholder — replace with final art, see Art Style Guide`.

Preferred approaches (in order):
1. **SVG files** — Simple shapes with project colors. Unity imports SVGs as sprites via the Vector Graphics package.
2. **Procedural runtime art** — C# scripts using `Texture2D` to generate sprites at runtime. Fast to iterate.
3. **Unity primitives** — Capsule, Quad, Sphere as 3D stand-ins.

### Design (디자인)
Owns how the game *feels* to play and how systems connect.

Produces:
- Scene layout and navigation flow
- UI/UX wireframes and interaction specs
- Unity scene hierarchy — which GameObjects, which components, parenting structure
- System wiring — how mechanics connect (input → movement → animation → sound → feedback)

### Code (코드)
Owns all implementation.

Produces:
- Unity project folder structure (see below)
- C# MonoBehaviour, ScriptableObject, and utility scripts
- Input handling, physics configuration, game state management
- Scene files with correct hierarchy and component setup
- Build-ready project

---

## Unity Project Structure

Always create projects with this layout:

```
GameName/
├── Assets/
│   ├── Scripts/
│   │   ├── Core/           # GameManager, SceneLoader, EventBus
│   │   ├── Gameplay/       # Player, Enemy, mechanics, pickups
│   │   ├── UI/             # HUD, menus, popups
│   │   ├── Data/           # ScriptableObject definitions, table loaders
│   │   └── Utils/          # Extensions, helpers, constants
│   ├── Scenes/
│   ├── Prefabs/
│   ├── Art/
│   │   ├── Sprites/
│   │   │   └── Placeholders/
│   │   ├── Materials/
│   │   └── Fonts/
│   ├── UI/
│   ├── Audio/
│   ├── Data/
│   │   ├── Tables/         # CSV 밸런스 테이블 (Excel에서 편집 가능)
│   │   └── ScriptableObjects/  # SO 인스턴스 (에디터에서 편집 가능)
│   └── _README/            # 폴더별 안내 문서
├── Packages/
│   └── manifest.json
└── ProjectSettings/
    └── (key settings files)
```

### 폴더 README 규칙

모든 주요 폴더에 `_README.md`를 생성한다. 나중에 사람이 폴더를 열었을 때 바로 파악할 수 있어야 한다.

각 README에 포함할 내용:
- 이 폴더에 뭐가 들어있는지
- 파일을 추가/수정할 때 따라야 할 규칙
- 관련된 다른 폴더나 스크립트 링크

예시 (`Assets/Scripts/Gameplay/_README.md`):
```
# Gameplay Scripts
플레이어, 적, 메카닉 관련 스크립트.

## 파일 추가 시
- MonoBehaviour는 하나의 책임만 갖도록 분리
- 적 관련 새 타입은 EnemyBase를 상속해서 만들 것
- 밸런스 수치는 여기 넣지 말고 Data/Tables/에서 관리

## 관련 위치
- 밸런스 수치: Assets/Data/Tables/
- 프리팹: Assets/Prefabs/Enemies/
```

---

## Workflow

### Starting a new project

1. Get the Project Brief (Step 0)
2. **Planning** drafts the GDD and feature list
3. All four layers review — each surfaces concerns or suggestions
4. **Director** makes final scope/priority call
5. **에셋 & 패키지 체크** (아래 참고) — 작업 시작 전에 한 번만
6. Begin implementation in sprints

### 에셋 & 패키지 체크

GDD와 우선순위가 확정된 직후, 작업을 시작하기 전에 한 번 묻는다. 개발 도중에 추가로 필요한 게 생기면 그때도 먼저 물어본다.

#### ⚠️ manifest.json 작성 전 반드시 실행: 패키지 버전 검증

`manifest.json`에 패키지를 추가하기 **전에** 다음 두 경로를 확인해서 실제 존재하는 버전만 사용한다.
사람에게 오류를 떠넘기지 않는다.

```bash
# 1. 로컬 캐시 확인 (이미 다운로드된 버전)
ls "C:/Users/user/AppData/Local/Unity/cache/packages/packages.unity.com/"

# 2. Unity 에디터 내장 패키지 목록 확인
ls "C:/Program Files/Unity/Hub/Editor/{버전}/Editor/Data/Resources/PackageManager/BuiltInPackages/"

# 3. 내장 패키지의 정확한 버전 확인 (package.json 직접 읽기)
cat "C:/Program Files/Unity/Hub/Editor/{버전}/Editor/Data/Resources/PackageManager/BuiltInPackages/{패키지명}/package.json"
```

**규칙:**
- 캐시에 있는 버전 → 그 버전 그대로 사용
- 캐시에 없는 패키지 → 내장 패키지 폴더에서 `package.json`을 읽어 정확한 버전 확인 후 사용
- 절대 버전을 추측하지 않는다. 반드시 파일을 읽어서 확인한다.
- `com.unity.modules.*` 는 Unity 내장이므로 버전 `"1.0.0"` 고정 사용 (항상 안전)
- `com.unity.modules.animation` — Animator 사용 시 필수. 자주 빠뜨림 주의
- URP(`com.unity.render-pipelines.universal`) — Light2D 사용 시 필수, 내장 패키지이므로 package.json에서 버전 읽기

**자주 발생하는 버전 오류 패턴:**
- `com.unity.2d.animation` — 메이저 버전이 Unity 버전마다 다름, 반드시 캐시 확인
- `com.unity.cinemachine` — 2.x vs 3.x API가 완전히 다름, 캐시에 있는 버전 사용
- `com.unity.2d.pixel-perfect`, `com.unity.2d.tilemap.extras` — 캐시에 없으면 제외

**확인 방식:**
기획/아트/디자인/코드 레이어가 필요한 에셋과 패키지를 각자 제안하면, 디렉터가 정리해서 아래 형태로 사람에게 제시한다.

```
[에셋 & 패키지 제안]

📦 Unity 패키지 (Package Manager에서 설치)
- Input System — 새 입력 시스템 (키보드/게임패드 통합)
  → 이미 있으신가요, 아니면 추가할까요?
- Cinemachine — 카메라 추적/전환 자동화
  → 이미 있으신가요, 아니면 추가할까요?
- TextMeshPro — UI 텍스트 품질 향상 (권장)
  → 이미 있으신가요, 아니면 추가할까요?

🛒 Asset Store (유료/무료)
- (예) Feel / DOTween — 주스감 있는 이펙트와 트윈 애니메이션
  → 보유하고 계신가요? 없으면 직접 구현하는 방향으로 갈게요.

🎨 아트 에셋
- (예) 이 장르에 맞는 무료 폰트, 사운드 팩 추천
  → 사용할까요, 아니면 다른 걸 갖고 계신가요?

없는 건 직접 구현하거나 대안으로 대체할게요.
필요 없는 항목은 건너뛰어도 됩니다.
```

**규칙:**
- 없어도 작업이 진행될 수 있으면 대안을 함께 제시한다 (예: DOTween 없으면 Coroutine으로 구현)
- 유료 에셋은 "있으신가요?" 먼저 묻고, 없으면 무료/직접 구현 대안 제안
- 한 번에 너무 많이 묻지 말 것 — 해당 스프린트에 실제로 필요한 것만 물어본다
- 작업 도중 새 패키지가 필요해지면 바로 멈추고 먼저 확인한다

**자주 쓰는 패키지 빠른 참조** (`references/packages.md` 참고)

### C# 코드 작성 규칙 (컴파일 오류 방지)

코드 레이어가 .cs 파일을 작성할 때마다 반드시 지키는 규칙. 사람이 오류를 보고 알려줄 때까지 기다리지 않는다.

#### ① using 지시문 필수 체크리스트

파일에서 아래 타입을 사용하면 대응하는 using이 있어야 한다. 작성 전에 반드시 확인.

| 사용하는 타입 | 필요한 using |
|---|---|
| `Light2D` | `using UnityEngine.Rendering.Universal;` |
| `GraphicsSettings`, `RenderPipelineAsset` | `using UnityEngine.Rendering;` |
| `UniversalRenderPipelineAsset`, `UniversalRendererData` | `using UnityEngine.Rendering.Universal;` |
| `Keyboard`, `Mouse`, `InputAction`, `InputSystem` | `using UnityEngine.InputSystem;` |
| `SceneManager` (비정규화) | `using UnityEngine.SceneManagement;` |
| `Image`, `Button`, `Slider`, `Canvas`, `CanvasScaler`, `GraphicRaycaster`, `Toggle`, `ScrollRect` | `using UnityEngine.UI;` |
| `TMP_Text`, `TextMeshProUGUI`, `TMP_InputField`, `TMP_FontAsset`, `AtlasPopulationMode` | `using TMPro;` |
| `GlyphRenderMode` | `using UnityEngine.TextCore.LowLevel;` |
| `Tilemap`, `TilemapRenderer`, `TilemapCollider2D` | `using UnityEngine.Tilemaps;` |
| `NavMeshAgent` | `using UnityEngine.AI;` |
| `EditorSceneManager`, `NewSceneSetup` | `using UnityEditor.SceneManagement;` |
| `AssetDatabase`, `EditorUtility`, `PrefabUtility`, `SerializedObject` | `using UnityEditor;` |

**예외:** `UnityEngine.InputSystem.Keyboard.current` 처럼 완전한 경로로 쓰면 using 불필요.

#### ② Unity 6: `[SerializeField]` 필드에 `?.` 사용 금지

Unity 6에서 미할당 `[SerializeField]` UnityEngine.Object에 `?.`를 쓰면 `UnassignedReferenceException` 발생.
C#의 `?.`는 Unity의 커스텀 `==` null 체크를 우회하기 때문.

```csharp
// ❌ Unity 6에서 [SerializeField] 필드에 절대 금지
myPanel?.SetActive(true);
myButton?.onClick.AddListener(Handler);
myAnimator?.SetTrigger("Play");

// ✅ 항상 이 패턴 사용
if (myPanel != null) myPanel.SetActive(true);
if (myButton != null) myButton.onClick.AddListener(Handler);
if (myAnimator != null) myAnimator.SetTrigger("Play");
```

**예외 — `?.`가 안전한 경우:**
- `SomeSingleton.Instance?.Method()` — static C# 참조, true null 반환
- `GetComponent<T>()?.Method()` — 없으면 true null 반환
- C# 델리게이트: `action?.Invoke()`
- C# 기본 타입: `value?.ToString()`

#### ③ 에디터 스크립트 블로킹 UI 금지

MCP 툴로 MenuItem을 실행할 때 Unity가 다이얼로그를 표시하면 Unity 세션 전체가 block되어 이후 모든 MCP 툴 호출이 timeout/fail된다.

| 금지 | 대체 |
|---|---|
| `EditorUtility.DisplayDialog(...)` | `Debug.Log("[완료]")` |
| 완료/확인 팝업 | `Debug.Log()` 또는 `Debug.LogWarning()` |

**규칙:** 에디터 스크립트(MenuItem, Editor 전용)에서 사용자 클릭을 기다리는 블로킹 UI를 절대 사용하지 않는다. 결과는 항상 Console 로그로만 출력한다.

#### ④ Unity 6 deprecated API — 절대 사용 금지

| 금지 | 대체 |
|---|---|
| `FindObjectOfType<T>()` | `FindFirstObjectByType<T>()` |
| `FindObjectsOfType<T>()` | `FindObjectsByType<T>(FindObjectsSortMode.None)` |
| `rb.velocity` (Rigidbody2D) | `rb.linearVelocity` |
| `rb.velocity` (Rigidbody) | `rb.linearVelocity` |

#### ④ SerializeField ↔ 씬/프리팹 생성 일관성 의무 확인

스크립트에 `[SerializeField]` 필드를 선언하면, 그 필드를 사용하는 씬/프리팹을 생성하는 에디터 스크립트에서 **반드시** 해당 오브젝트를 만들고 `SetField()`로 연결해야 한다.

**체크 방법:** 에디터 스크립트(ProjectInitializer 등)를 작성할 때, 씬에 추가하는 컴포넌트의 모든 `[SerializeField]` 필드 목록과 실제 생성/연결 코드를 1:1로 대조한다.

예시:
```
MainMenuController의 [SerializeField] 필드:
  newGameButton    → MakeUIButton(...) + SetField(mmc, "newGameButton", ...) ✓
  continueButton   → MakeUIButton(...) + SetField(mmc, "continueButton", ...) ✓
  titleAnimator    → titleGO.AddComponent<Animator>() + SetField(...) ✓
  mainPanel        → MakeUIPanel(...) + SetField(mmc, "mainPanel", ...) ✓
  settingsPanel    → MakeUIPanel(...) + SetField(mmc, "settingsPanel", ...) ✓
  ...
```

연결하지 않은 필드가 하나라도 있으면 씬 생성 코드를 완성하기 전까지 다음 단계로 넘어가지 않는다.

#### ⑤ 에디터 스크립트 — 반드시 절대 경로 사용

Editor 스크립트에서 `File.WriteAllBytes`, `Directory.CreateDirectory` 등 파일 I/O를 쓸 때 **반드시 절대 경로**를 사용한다.

```csharp
// ❌ 상대 경로 — 현재 작업 디렉터리에 따라 실패 가능
Directory.CreateDirectory("Assets/Art/Sprites/");
File.WriteAllBytes("Assets/Art/Sprites/foo.png", data);

// ✅ Application.dataPath 기반 절대 경로
string absPath = Application.dataPath + "/Art/Sprites/";
Directory.CreateDirectory(absPath);
File.WriteAllBytes(absPath + "foo.png", data);

// ✅ AssetDatabase API는 "Assets/..." 상대 경로 사용 (이건 OK)
AssetDatabase.LoadAssetAtPath<Sprite>("Assets/Art/Sprites/foo.png");
```

**규칙:** 파일 I/O → `Application.dataPath` 절대 경로 / AssetDatabase API → `"Assets/..."` 상대 경로. 혼용 금지.

#### ⑥ 런타임 Texture2D 스프라이트는 프리팹에 저장 불가

`new Texture2D()` + `Sprite.Create()`로 만든 스프라이트는 메모리에만 존재하며, Unity 재시작 시 소실된다. 프리팹/씬에 직접 할당해도 재시작 후 "Missing" 레퍼런스가 된다.

**해결책:** 절대로 런타임 생성 Texture2D를 프리팹/씬 참조로 저장하지 않는다. 대신:
1. `tex.EncodeToPNG()` → `File.WriteAllBytes()` 로 PNG 저장
2. `AssetDatabase.Refresh()` + `TextureImporter` 설정 (Sprite, PixelsPerUnit, FilterMode.Point)
3. `AssetDatabase.LoadAssetAtPath<Sprite>()` 로 임포트된 에셋 로드 후 프리팹에 할당

**씬 인스턴스 주의:** 프리팹을 업데이트해도, 씬 인스턴스에 "Missing" 스프라이트가 Override로 남아있으면 상속되지 않는다. `PrefabUtility.RevertPropertyOverride()` 또는 씬 내 SpriteRenderer를 직접 수정해야 한다.

#### ⑦ Tilemap — 컴포넌트만 있어도 타일 미배치 시 플레이 불가

Tilemap + TilemapCollider2D + CompositeCollider2D 셋업이 완료되어도, 실제 타일(Tile 에셋)이 `tilemap.SetTile()`로 페인팅되지 않으면 바닥이 없어 플레이어가 낙하한다.

**체크리스트:**
- `Tilemap` 컴포넌트의 `cellBounds.size.x > 0` 인지 확인 (0이면 빈 타일맵)
- `CompositeCollider2D.pathCount > 0` 인지 확인 (0이면 충돌 없음)
- 레벨 씬 생성 시 반드시 `tilemap.SetTile()` 으로 기본 바닥 페인팅 포함

#### ⑧ MonoBehaviour 상속 — lifecycle 메서드 반드시 override

Unity MonoBehaviour를 상속할 때 `Start()`, `Awake()`, `Update()` 등을 재정의할 때는
**반드시 `override` 키워드**를 붙여야 한다. 없으면 C# method hiding이 발생해 부모의
lifecycle 메서드가 **완전히 무시된다** — 컴파일 에러 없이 런타임에 조용히 실패.

```csharp
// ❌ hiding — EnemyBase.Start()가 절대 호출되지 않음 (컴파일은 통과)
public class BossBase : EnemyBase
{
    void Start() { /* base.Start() 없음 → player == null */ }
}

// ✅ 올바른 override + base 호출
public class BossBase : EnemyBase
{
    protected override void Start()
    {
        base.Start();  // ← 부모 Start() 반드시 호출
        // 추가 로직
    }
}
```

**자체 검증:**
```bash
grep -rn "void Start\(\)\|void Awake\(\)\|void Update\(\)\|void OnEnable\(\)" Assets/Scripts/
```
`override` 없는 결과가 있으면 상속 계층 확인.

#### ⑨ DontDestroyOnLoad 싱글턴 — BootLoader 등록 체크리스트

새 DontDestroyOnLoad 싱글턴을 만들 때마다 **BootLoader(또는 초기화 시스템)에 즉시 등록**한다.
등록하지 않으면 Boot 씬을 거치지 않고 레벨 씬을 직접 열 때 `Instance == null`.

```csharp
// BootLoader.cs — 새 싱글턴 추가 시 이 목록에 반드시 추가
EnsureInstance<GameManager>(...);
EnsureInstance<SaveSystem>(...);
EnsureInstance<UpgradeManager>(...);  // ← 빠뜨리기 쉬운 패턴
```

**체크 방법:** `DontDestroyOnLoad`를 grep해 목록 추출 → BootLoader의 EnsureInstance 목록과 1:1 대조.

#### ⑩ 저장(Save) / 로드(Load) 대칭성 규칙

`Save()`에서 직렬화하는 모든 값은 `LoadFromSave()`에서 **반드시 복원**해야 한다.
`bool` 플래그는 잘 복원하면서 파생 숫자값(count, amount)을 놓치는 패턴이 빈번하다.

```csharp
// ❌ 비대칭 — bool은 복원하고 ExtraHearts는 누락
UpgradedLightPulse = save.upgradeLightPulse;  // 복원됨
// ExtraHearts 복원 없음 → 재시작 후 0으로 초기화, 중복 구매 가능

// ✅ 대칭 복원
UpgradedLightPulse = save.upgradeLightPulse;
ExtraHearts = Mathf.Max(0, save.maxHealth - 3);  // 파생값 직접 계산
```

**저장/로드 대칭 체크리스트:**
- bool 플래그 저장 → bool 플래그 복원 ✓
- count/amount 저장 → count/amount 복원 ✓ (bool에서 역산 가능하면 역산)
- 최대값 저장 → 최대값 복원 + 현재값도 Clamp ✓

#### ⑪ Update()에서 position 직접 설정 + 이동 로직 공존 금지

같은 Update()에서 `transform.position = 고정값` (idle bob 등) 이후 `MoveTowards/Lerp`로
이동하면 매 프레임 원점으로 복귀해 **이동이 무효화**된다.

```csharp
// ❌ bob이 MoveTowards를 매 프레임 덮어씀
transform.position = startPos + bob;      // ← 원점 강제
transform.position = Vector3.MoveTowards(transform.position, target, speed);  // 무효

// ✅ 상태에 따라 둘 중 하나만 실행
if (isAttracted)
{
    transform.position = Vector3.MoveTowards(transform.position, target, speed);
    startPos = transform.position;  // ← 기준점 갱신 필수
}
else
{
    transform.position = startPos + bob;
}
```

#### ⑧ ProjectInitializer 재실행 안전 보장

초기화 스크립트는 항상 두 번 이상 실행된다고 가정하고 작성한다.

- **씬 저장 전 반드시** `CloseSceneIfOpen(path)` 호출 — 이미 열린 씬에 SaveScene하면 오류
- **TMP 사용 시** 초기화 첫 단계에서 `SetupTMPEssentials()` 호출 — Essential Resources 없으면 TextMeshProUGUI 렌더링 불가
- **한글 UI 사용 시** Windows Malgun Gothic에서 TMP Dynamic Font Asset 생성 후 모든 TMP 컴포넌트에 적용 — 기본 TMP 폰트는 한글 미지원
- **UI 씬 생성 시** `EventSystem` + `InputSystemUIInputModule` 반드시 추가 — 없으면 버튼 클릭 불가
- **패키지/에셋 생성 전** `if (File.Exists(path)) return;` 가드로 중복 생성 방지

#### ⑤ 스프린트 완료 전 의무 자체 검증

파일을 모두 작성한 뒤, 사람에게 넘기기 전에 직접 grep으로 확인:

```
# deprecated API 잔존 확인
grep -rn "FindObjectOfType\b" Assets/Scripts/
grep -rn "\.velocity\s*[=+]" Assets/Scripts/

# 패키지 의존 타입이 using 없이 쓰이는지 확인
grep -rn "\bLight2D\b" Assets/Scripts/ | grep -v "using UnityEngine.Rendering"
grep -rn "\bKeyboard\.current\b" Assets/Scripts/ | grep -v "UnityEngine\.InputSystem\."
```

오류 발견 시 즉시 수정하고 다시 검증. 사람에게 "오류가 있을 수 있습니다"라고 넘기지 않는다.

---

### Each feature or sprint

First, decide: sequential or parallel?

- **Sequential** when one layer's output feeds another (e.g., Planning defines a mechanic → Design specifies the system → Code implements it)
- **Parallel** when layers can work independently (e.g., Art makes placeholder sprites while Code sets up project structure and Planning drafts the next mechanic)

For each task:
1. Assign to the relevant layer(s)
2. Layer produces output
3. Other layers give brief feedback — flag issues, suggest improvements
4. Director synthesizes and decides
5. Implementing layer applies the decision

### Human checkpoints

After every major decision or deliverable, stop and present:

```
[CHECKPOINT]
완료: ...
결정사항: ...
다음 작업: ...
→ 계속 진행할까요? 수정할 부분이 있으면 말씀해주세요.
```

The human can:
- Say "계속" or "continue" to proceed
- Modify any layer's output directly
- Override a Director decision
- Take over a specific layer entirely for any stretch of work

Never block on the human — but always make it obvious where they can intervene. If the human takes over a layer, hand off cleanly and resume from wherever they leave off.

---

## Director Decision Protocol

When layers have weighed in on something that needs a decision:

```
[DIRECTOR]
기획: (Planning's position)
아트: (Art's position)
디자인: (Design's position)
코드: (Code's position, especially technical constraints)
→ 결정: (what we're doing and why)
이의 있으신가요?
```

Keep decisions lean. A good Director decision:
- Unblocks the most work with the fewest tradeoffs
- Respects hard technical constraints (Code has veto power over things that are genuinely impossible)
- Can be revisited if new information emerges

If a layer has a blocking concern after the decision, surface it explicitly before moving on.

---

## Data Tables & Balancing

게임 수치(밸런스)는 코드와 분리해서 관리한다. 사람이 코드를 건드리지 않고 수치를 조정할 수 있어야 한다.

### 테이블 관리 방식

**두 가지 방식을 병행:**

1. **CSV 테이블** (`Assets/Data/Tables/`) — Excel/스프레드시트로 편집 가능
   - `enemies.csv` — 적 스탯
   - `items.csv` — 아이템/픽업 효과
   - `levels.csv` — 레벨별 난이도 파라미터
   - `weapons.csv` — 무기/스킬 수치

2. **ScriptableObject** (`Assets/Data/ScriptableObjects/`) — Unity 에디터에서 슬라이더/인스펙터로 편집
   - 전역 게임 설정 (GameConfig)
   - 개별 캐릭터/아이템 프리셋

### CSV 테이블 예시 형식

`enemies.csv`:
```
id,name,hp,speed,damage,score_reward,spawn_weight
enemy_001,Slime,30,2.0,5,10,0.5
enemy_002,Goblin,50,3.5,12,25,0.3
enemy_003,Boss,300,1.5,30,200,0.0
```

`items.csv`:
```
id,name,type,value,duration,spawn_weight
item_001,HealthPack,heal,20,0,0.4
item_002,SpeedBoost,buff,0,5.0,0.3
item_003,Shield,buff,0,3.0,0.2
```

### CSV 로더 패턴

```csharp
// Assets/Scripts/Data/TableLoader.cs
// 런타임에 CSV를 읽어 딕셔너리로 제공 — 코드 재빌드 없이 수치 변경 가능
public class TableLoader : MonoBehaviour
{
    public static Dictionary<string, EnemyData> Enemies { get; private set; }

    void Awake()
    {
        Enemies = LoadTable<EnemyData>("enemies");
    }

    private static Dictionary<string, T> LoadTable<T>(string tableName) where T : ITableRow, new()
    {
        var result = new Dictionary<string, T>();
        var csv = Resources.Load<TextAsset>($"Tables/{tableName}");
        if (csv == null) return result;

        var lines = csv.text.Split('\n');
        var headers = lines[0].Trim().Split(',');

        for (int i = 1; i < lines.Length; i++)
        {
            if (string.IsNullOrWhiteSpace(lines[i])) continue;
            var values = lines[i].Trim().Split(',');
            var row = new T();
            row.FromCSV(headers, values);
            result[row.Id] = row;
        }
        return result;
    }
}
```

### 밸런싱 체크포인트

밸런스 수치를 결정하거나 수정할 때는 테이블로 표현해서 사람이 한눈에 비교할 수 있게 제시한다:

```
[밸런스 제안 — 기획 레이어]
| 적       | HP  | 속도 | 데미지 | 점수 |
|---------|-----|-----|-------|-----|
| Slime   | 30  | 2.0 | 5     | 10  |
| Goblin  | 50  | 3.5 | 12    | 25  |
| Boss    | 300 | 1.5 | 30    | 200 |

조정이 필요한 부분이 있으면 직접 수치를 알려주세요.
CSV 파일(Assets/Data/Tables/enemies.csv)에서도 직접 편집 가능합니다.
```

---

## Reference Files

Pull these in when starting the relevant work — don't load all of them upfront:

- `references/gdd-template.md` — Game Design Document template
- `references/unity-patterns.md` — Common Unity C# patterns (Singleton, State Machine, Object Pool, Event System, ScriptableObject architecture)
- `references/patterns-3d.md` — 3D 전용 패턴 (CharacterController, NavMesh AI, 3D 카메라, 플레이스홀더 팩토리)
- `references/art-guide-template.md` — Template for defining art style, palette, and placeholder conventions
- `references/table-schemas.md` — 게임 타입별 테이블 스키마 모음 (RPG, 플랫포머, 슈터 등)
- `references/packages.md` — Unity 패키지 & 에셋 빠른 참조
- `references/unity6-known-issues.md` — Unity 6 실제 발견된 버그 & 우회법 (TilemapCollider, URP 2D 스크린샷, SpriteImport 등)
