# Art Style Guide Template

## 프로젝트 비주얼 아이덴티티

**스타일**: (예: 미니멀 플랫, 픽셀아트 16x16, 로우폴리 3D, 카툰 아웃라인)
**무드**: (예: 어둡고 긴장감 / 밝고 경쾌 / 차갑고 SF적)
**레퍼런스 게임**: (예: Hollow Knight, Celeste, Among Us)

---

## 컬러 팔레트

| 용도 | 색상 이름 | HEX | RGB |
|------|----------|-----|-----|
| 주 배경 | Background | #1a1a2e | 26, 26, 46 |
| 주 색상 | Primary | #e94560 | 233, 69, 96 |
| 보조 색상 | Secondary | #0f3460 | 15, 52, 96 |
| 강조 색상 | Accent | #f5a623 | 245, 166, 35 |
| 텍스트 | Text | #ffffff | 255, 255, 255 |
| 위험/적 | Danger | #ff4444 | 255, 68, 68 |

팔레트는 최대 6~8색으로 제한. 색을 추가하고 싶을 때는 기존 색의 명도/채도 변형 우선.

---

## Shape Language (형태 언어)

- **플레이어**: (예: 둥근 모서리 → 친근함 / 날카로운 삼각형 → 공격성)
- **적**: (예: 각지고 날카로운 형태)
- **환경/배경**: (예: 직사각형, 수직/수평 강조)
- **아이템/픽업**: (예: 원형, 빛나는 느낌)

---

## 플레이스홀더 아트 규칙

1. 모든 플레이스홀더는 프로젝트 컬러 팔레트 사용 (회색 박스 금지)
2. 파일명에 `_ph` 접미사 (예: `player_idle_ph.svg`)
3. 저장 위치: `Assets/Art/Sprites/Placeholders/`
4. 코드 내 주석: `// Placeholder — replace with final art`

### SVG 플레이스홀더 예시

**플레이어 (32x32 원형):**
```svg
<svg width="32" height="32" xmlns="http://www.w3.org/2000/svg">
  <circle cx="16" cy="16" r="14" fill="#e94560" stroke="#ffffff" stroke-width="2"/>
  <!-- Placeholder player — replace with final art -->
</svg>
```

**적 (32x32 사각형):**
```svg
<svg width="32" height="32" xmlns="http://www.w3.org/2000/svg">
  <rect x="2" y="2" width="28" height="28" fill="#0f3460" stroke="#ff4444" stroke-width="2"/>
</svg>
```

**아이템/픽업 (16x16 다이아몬드):**
```svg
<svg width="16" height="16" xmlns="http://www.w3.org/2000/svg">
  <polygon points="8,1 15,8 8,15 1,8" fill="#f5a623"/>
</svg>
```

---

## UI 비주얼 언어

- **폰트**: (예: 제목 - 굵은 고딕, 본문 - 가독성 우선 산세리프)
- **버튼 스타일**: (예: 테두리만 있는 아웃라인 버튼 / 채워진 솔리드 버튼)
- **HUD 위치**: HP — 좌상단 / 점수 — 우상단 / 미니맵 — 우하단
- **아이콘 크기**: 최소 32x32px (모바일은 64x64px)

---

## 애니메이션 인텐트

실제 애니메이션 파일은 나중에 만들더라도, 어떤 애니메이션이 필요한지 먼저 정의:

| 오브젝트 | 애니메이션 | 트리거 | 프레임 수(예상) |
|---------|-----------|--------|--------------|
| Player | Idle | 항상 | 4 |
| Player | Run | 이동 중 | 8 |
| Player | Jump | 점프 시 | 3 |
| Player | Death | HP = 0 | 6 |
| Enemy | Idle | 항상 | 4 |
| Enemy | Attack | 플레이어 감지 | 5 |
