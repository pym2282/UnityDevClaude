# 게임 타입별 테이블 스키마

장르에 맞는 테이블을 선택해서 사용. 필요한 컬럼만 남기고 나머지는 삭제해도 됨.

---

## 공통 테이블 (모든 장르)

### enemies.csv
```
id, name, hp, max_hp, speed, damage, armor, score_reward, spawn_weight, drop_table_id
```

### items.csv
```
id, name, type, rarity, heal_amount, damage_bonus, speed_bonus, duration, description, spawn_weight
```
- type: `heal` / `buff` / `weapon` / `key` / `currency`
- rarity: `common` / `rare` / `epic` / `legendary`

### levels.csv
```
level_id, scene_name, enemy_spawn_rate, max_enemies, time_limit, unlock_condition, background_id
```

### audio.csv
```
id, clip_name, type, volume, pitch_min, pitch_max, loop
```
- type: `sfx` / `bgm` / `ambient`

---

## 플랫포머 추가 테이블

### player_abilities.csv
```
id, name, unlock_level, jump_force, speed_multiplier, damage_multiplier, cooldown, stamina_cost
```

### platforms.csv
```
id, type, move_speed, move_distance, fall_delay, respawn_time
```
- type: `static` / `moving` / `falling` / `bouncy` / `disappearing`

### checkpoints.csv
```
id, scene_name, position_x, position_y, unlock_item_id
```

---

## 슈터/액션 추가 테이블

### weapons.csv
```
id, name, damage, fire_rate, bullet_speed, ammo_max, reload_time, spread, projectile_id, unlock_cost
```

### projectiles.csv
```
id, name, speed, damage, pierce_count, bounce_count, lifetime, aoe_radius, vfx_id
```

### waves.csv
```
wave_id, level_id, enemy_id, count, spawn_interval, formation, delay_from_previous
```

---

## RPG/어드벤처 추가 테이블

### characters.csv
```
id, name, class, base_hp, base_attack, base_defense, base_speed, exp_to_next_level
```

### skills.csv
```
id, name, class, damage_multiplier, heal_amount, cooldown, mp_cost, range, target_type, description
```
- target_type: `self` / `single` / `aoe` / `all_enemies`

### quests.csv
```
id, title, description, objective_type, objective_target_id, objective_count, reward_exp, reward_item_id, prereq_quest_id
```

### dialogue.csv
```
id, speaker_id, text_kr, text_en, next_dialogue_id, condition_flag, trigger_flag
```

### shop.csv
```
id, item_id, price, currency_type, stock, restock_condition
```

---

## 퍼즐 추가 테이블

### puzzles.csv
```
id, scene_name, type, difficulty, time_limit, hint_text, solution_key, reward_id
```

### progression.csv
```
id, unlock_type, unlock_id, required_puzzles, required_items
```

---

## 밸런싱 팁

**적 밸런싱 기준점:**
- 플레이어가 죽기 전 처치 가능한 적 수 = 목표 전투 길이 기준으로 역산
- 보스 HP = 일반 적 평균 HP × 10 (짧은 게임) ~ × 30 (긴 게임)
- 적 데미지 = 플레이어 최대 HP / (원하는 히트 수)

**경제 밸런싱 기준점:**
- 아이템 가격은 해당 아이템으로 버는 점수/골드의 3~5배
- 희귀도별 드롭률: common 50% / rare 30% / epic 15% / legendary 5%

**난이도 곡선:**
- 레벨마다 적 HP +10~15%, 속도 +5%가 일반적인 시작점
- 3~4레벨마다 새 적 타입 등장으로 신선함 유지
