| 날짜 | 스킬 | 수정 내용 | 유형 |
|------|------|-----------|------|
| 2026-03-22 | unity-qa-tester | Application.runInBackground=true 필수 패턴 추가 (frameCount=1 멈춤 진단), SpriteRenderer.sprite==null 캐릭터 미표시 진단 패턴 추가 (⑤~⑥) | 구조적 수정 |
| 2026-03-22 | unity-qa-tester | ⑤ WaitForSecondsRealtime Unity 6 DontDestroyOnLoad 버그 패턴 추가, find_gameobjects DDOL 미검색 주의사항 추가, Editor MenuItem 방식 테스트 가이드 추가 | 구조적 수정 |
| 2026-03-22 | unity-qa-tester | 2단계 정적 검사에 반복 발생 버그 패턴 체크리스트 추가 (lifecycle method hiding, DontDestroyOnLoad 등록 누락, Save/Load 비대칭, Update position override 충돌) | 구조적 수정 |
| 2026-03-22 | unity-game-director | C# 코드 작성 규칙에 ⑧~⑪ 추가 (MonoBehaviour lifecycle override, BootLoader DontDestroyOnLoad 체크리스트, Save/Load 대칭 의무 확인, Update position 충돌 방지) | 구조적 수정 |
