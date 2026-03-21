# Unity 패키지 & 에셋 빠른 참조

## Unity 공식 패키지 (Package Manager)

### 거의 항상 필요
| 패키지 | 용도 | 설치 방법 |
|--------|------|----------|
| Input System | 키보드/마우스/게임패드 통합 입력 | Package Manager → Input System |
| TextMeshPro | UI 텍스트 고품질 렌더링 | Package Manager → TextMeshPro |
| Cinemachine | 카메라 추적, 전환, 흔들림 | Package Manager → Cinemachine |

### 장르별로 자주 필요
| 패키지 | 용도 | 장르 |
|--------|------|------|
| 2D Animation | 뼈대(Bone) 기반 2D 애니메이션 | 2D 전반 |
| 2D Tilemap Extras | 타일맵 고급 기능 | 플랫포머, 탑다운 |
| Navmesh Components | AI 경로찾기 | 탑다운, RPG |
| Visual Effect Graph | GPU 파티클 이펙트 | 액션, 슈터 |
| Shader Graph | 비주얼 셰이더 편집 | 모든 장르 |
| Unity UI (uGUI) | 기본 UI 시스템 | 모든 장르 |
| UI Toolkit | 새 UI 시스템 (에디터 툴 포함) | 모든 장르 |
| Vector Graphics | SVG 임포트 | 아트 에셋 |
| Addressables | 에셋 번들 관리, 메모리 최적화 | 중~대형 프로젝트 |
| Localization | 다국어 지원 | 출시 목표 프로젝트 |

---

## Asset Store — 무료 추천

### 이펙트 & 주스감
| 에셋 | 용도 | 비고 |
|------|------|------|
| DOTween (HOTween v2) | 트윈 애니메이션, UI 모션 | 무료 버전으로 대부분 가능 |
| Lean Tween | 경량 트윈 라이브러리 | 무료 |
| Unity Particle Pack | 기본 파티클 이펙트 모음 | Unity 공식 무료 |

### 아트 & UI
| 에셋 | 용도 | 비고 |
|------|------|------|
| Kenney Assets | 픽셀아트/플랫 스타일 스프라이트 대량 | kenney.nl에서 무료 |
| Google Fonts | 게임 UI용 폰트 | fonts.google.com |
| Free Pixel Art Fonts | 레트로 스타일 폰트 | itch.io |

### 사운드
| 에셋 | 용도 | 비고 |
|------|------|------|
| Freesound.org | SFX 소재 | 라이선스 확인 필요 |
| Kenney Audio | 게임 SFX 팩 | 무료, CC0 |
| OpenGameArt | BGM + SFX | 무료, 라이선스 다양 |

---

## Asset Store — 유료 (가성비 좋은 것들)

| 에셋 | 용도 | 대략 가격대 |
|------|------|------------|
| Feel | 주스감, 히트스탑, 화면 흔들림 등 | ~$50 |
| Odin Inspector | 에디터 인스펙터 강화, 직렬화 | ~$55 |
| Rewired | 고급 입력 시스템 (게임패드 완벽 지원) | ~$40 |
| A* Pathfinding Project | 고성능 경로찾기 | 무료/Pro $100 |
| PlayFab SDK | 백엔드, 리더보드, 저장 | 무료 티어 있음 |

---

## 대안 매핑 (유료 없을 때)

| 유료 에셋 | 직접 구현 대안 |
|----------|--------------|
| DOTween | `IEnumerator` Coroutine + `Mathf.Lerp` |
| Feel (히트스탑) | `Time.timeScale` 단시간 조작 |
| Feel (화면 흔들림) | Cinemachine Impulse Source |
| Odin Inspector | `[Header]`, `[Range]`, Custom Editor |
| A* Pathfinding | Unity 내장 NavMesh |

---

## manifest.json 작성 규칙

### ⚠️ 반드시 먼저 캐시 확인

```bash
ls "C:/Users/user/AppData/Local/Unity/cache/packages/packages.unity.com/"
ls "C:/Program Files/Unity/Hub/Editor/{버전}/Editor/Data/Resources/PackageManager/BuiltInPackages/"
```

캐시에 없는 버전은 절대 manifest에 넣지 않는다. 사람에게 오류를 넘기지 않는다.

### 안전한 기본 manifest (Unity 6000.4.0f1 기준, 캐시 검증 완료)

```json
{
  "dependencies": {
    "com.unity.inputsystem": "1.11.2",
    "com.unity.textmeshpro": "3.0.9",
    "com.unity.cinemachine": "2.10.3",
    "com.unity.2d.pixel-perfect": "5.0.3",
    "com.unity.2d.tilemap.extras": "3.1.3",
    "com.unity.modules.physics2d": "1.0.0",
    "com.unity.modules.audio": "1.0.0",
    "com.unity.modules.ui": "1.0.0",
    "com.unity.modules.particlesystem": "1.0.0",
    "com.unity.modules.tilemap": "1.0.0",
    "com.unity.modules.imageconversion": "1.0.0"
  }
}
```

**제외 이유:**
- `com.unity.ugui` — Unity 6에 기본 내장, 별도 명시 불필요
- `com.unity.render-pipelines.universal` — Unity 6에 기본 내장
- `com.unity.2d.animation` — 버전마다 API 다름, 필요 시 캐시 확인 후 추가

버전은 Unity 버전에 따라 다르므로, 다른 Unity 버전 사용 시 반드시 캐시를 먼저 확인한다.
