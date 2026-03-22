| 날짜 | 스킬 | 수정 내용 | 유형 |
|------|------|-----------|------|
| 2026-03-23 | unity-game-director | ⑮ 절차적 UI 씬 필수 GO 목록 추가 (EventSystem, AudioListener, font 연결) | 구조적 수정 |
| 2026-03-23 | unity-qa-tester | ⑨ TMP_FontAsset font 미연결 → 한글 미표시 패턴 추가, UI 버튼 체크리스트에 font 항목 추가 | 구조적 수정 |
| 2026-03-23 | unity-game-director | ⑮ UI 버튼 클릭 가능 여부 체크리스트 추가 (EventSystem, GraphicRaycaster, 오버레이, SerializeField 연결, AddListener) | 구조적 수정 |
| 2026-03-23 | unity-qa-tester | UI 버튼 클릭 동작 확인 체크리스트를 3단계 플레이테스트 시나리오에 추가 | 구조적 수정 |
| 2026-03-23 | unity-game-director | ⑭ 씬 직접 추가 vs 런타임 동적 생성 구분 규칙 추가 | 구조적 수정 |
| 2026-03-23 | unity-qa-tester | ⑧ EventSystem 누락 버그 패턴 추가 (씬 인프라 GO는 씬에 직접 추가) | 구조적 수정 |
| 2026-03-23 | unity-game-director | ⑬ 타 클래스 메서드 참조 확인(cross-reference check) 규칙 추가 — 스프린트 완료 전 자체 검증 섹션 | 구조적 수정 |
| 2026-03-23 | unity-qa-tester | ⑦ 타 클래스 메서드 참조 불일치 패턴 추가 — CS1061 컴파일 에러 진단 체크리스트 | 구조적 수정 |
| 2026-03-22 | unity-qa-tester | Application.runInBackground=true 필수 패턴 추가 (frameCount=1 멈춤 진단), SpriteRenderer.sprite==null 캐릭터 미표시 진단 패턴 추가 (⑤~⑥) | 구조적 수정 |
| 2026-03-22 | unity-qa-tester | ⑤ WaitForSecondsRealtime Unity 6 DontDestroyOnLoad 버그 패턴 추가, find_gameobjects DDOL 미검색 주의사항 추가, Editor MenuItem 방식 테스트 가이드 추가 | 구조적 수정 |
| 2026-03-22 | unity-qa-tester | 2단계 정적 검사에 반복 발생 버그 패턴 체크리스트 추가 (lifecycle method hiding, DontDestroyOnLoad 등록 누락, Save/Load 비대칭, Update position override 충돌) | 구조적 수정 |
| 2026-03-22 | unity-game-director | C# 코드 작성 규칙에 ⑧~⑪ 추가 (MonoBehaviour lifecycle override, BootLoader DontDestroyOnLoad 체크리스트, Save/Load 대칭 의무 확인, Update position 충돌 방지) | 구조적 수정 |
