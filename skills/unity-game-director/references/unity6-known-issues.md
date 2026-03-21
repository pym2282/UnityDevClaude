# Unity 6 Known Issues & Workarounds

이 문서는 Unity 6에서 실제로 발견된 버그와 우회법을 정리한다.
새로운 이슈를 발견하면 이 파일에 추가한다.

---

## 1. TilemapCollider2D + CompositeCollider2D 런타임 pathCount=0

**증상:** 에디터에서 타일을 페인팅하고 CompositeCollider2D.GenerateGeometry()를 호출해도,
플레이 모드 진입 시 `pathCount=0`, `shapeCount=0`으로 충돌이 생성되지 않음.
플레이어가 타일맵을 통과해 낙하.

**원인:** Unity 6에서 Static Rigidbody2D + CompositeCollider2D 조합에서
에디터에서 생성된 geometry가 런타임에 물리 월드에 등록되지 않는 버그.

**해결책:** `TilemapCollider2D.compositeOperation = None` (직접 사용)

```csharp
// 에디터 스크립트에서
var tilemapCollider = tilemap.GetComponent<TilemapCollider2D>();
tilemapCollider.compositeOperation = Collider2D.CompositeOperation.None;

// 런타임 Awake() 보험 — TilemapColliderInit.cs 컴포넌트로 추가
tilemapCollider.enabled = false;
tilemapCollider.enabled = true;  // 강제 shape 재빌드
```

**참고:** `TilemapColliderInit.cs` → `Assets/Scripts/Core/TilemapColliderInit.cs`

---

## 2. URP 2D 스크린샷 — manage_camera로 캡처 불가

**증상:** `manage_camera screenshot game_view`로 촬영하면 완전히 검은 화면.
URP 2D Light2D 렌더링이 카메라 RenderTexture 방식으로는 적용되지 않음.

**해결책:** `Tools/Capture Game View Full` 메뉴 사용 (ScreenCapture.CaptureScreenshot)
```
execute_menu_item("Tools/Capture Game View Full")
파일 위치: Assets/Screenshots/menu_capture.png
```

---

## 3. Sprite-Lit-Default 머티리얼 + Multiply 블렌드 = 매우 어두운 화면

**증상:** 게임이 거의 완전히 검게 보임. GlobalLight2D가 있어도 스프라이트가 안 보임.

**원인:** Renderer2D 블렌드 스타일 index 0이 Multiply로 설정된 경우,
GlobalLight2D color가 (0.4, 0.6, 0.8)이면 스프라이트 색상을 60%까지 어둡게 만듦.
어두운 팔레트(ForestGreen, VoidPurple)와 조합 시 배경과 거의 구별 불가.

**실제 게임 화면:** Tools/Capture Game View Full로 캡처해야 올바른 결과 확인 가능.
관리 카메라 캡처(manage_camera)는 URP 2D 조명을 반영하지 못함.

---

## 4. SpriteImportMode.Single 누락 시 Sprite 로드 실패

**증상:** `AssetDatabase.LoadAssetAtPath<Sprite>(path)` 반환값이 null.

**원인:** 스프라이트 PNG가 Multiple import mode로 설정된 경우 Single로 로드 불가.

**해결책:** TextureImporter 설정 시 반드시 명시:
```csharp
importer.spriteImportMode = SpriteImportMode.Single;
```

---

## 5. PrefabUtility.SaveAsPrefabAsset — SetDirty 필수

**증상:** `sr.sprite = sprite` 후 `SaveAsPrefabAsset()` 호출해도 prefab에 저장 안 됨.

**해결책:** 저장 전 `EditorUtility.SetDirty(go)` 반드시 호출.
```csharp
sr.sprite = sprite;
EditorUtility.SetDirty(go);   // ← 누락 시 저장 안 됨
PrefabUtility.SaveAsPrefabAsset(go, path);
```

---

## 6. Lumina 프로젝트 특이 설정

- `Physics2D.gravity.y = -28` (기본값 -9.81과 다름)
- `PlayerController.fallMultiplier = 2.5` → 낙하 시 실효 중력 -70
- URP Pipeline: `Assets/Settings/LuminaURPAsset.asset` → renderer = `LuminaRenderer2D` (Renderer2DData)
- 카메라 clearFlags=2 (Solid Color), backgroundColor=(0.04, 0.04, 0.10) = DeepDark navy
- 플레이어 스폰: x=-8, y=0 / 지면 상단: y=-1 / 낙하 거리: 0.55 units → 약 0.13초

---

## 7. 씬별 정보 (World1)

| 씬 | buildIndex | 특이사항 |
|---|---|---|
| Boot | 0 | 자동 로드 → MainMenu |
| MainMenu | 1 | TableLoader 로그로 정상 확인 |
| World1_Level1 | 2 | TilemapColliderInit 추가됨 |
| World1_Level2 | 3 | LuminaFixer로 페인팅됨 |
| World1_Level3 | 4 | LuminaFixer로 페인팅됨 |
| World1_Level4 | 5 | Boss 씬 |
