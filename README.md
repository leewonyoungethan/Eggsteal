# 🦊 여우의 달걀 도둑

농장의 새들이 둥지에서 **알을 품은 채 자고** 있습니다. 💤
알을 훔치는 순간 둥지 주인이 **깨어나서 쫓아옵니다.** 훔친 알을 왼쪽 아래 여우 굴까지 가져가세요. (Three.js, 단일 파일)

▶ 플레이: https://leewonyoungethan.github.io/Eggsteal/

로컬 실행: `index.html`을 브라우저로 열면 됩니다.

## 조작
| 키 | 동작 |
|---|---|
| WASD / 방향키 | 이동 |
| Shift | 살금살금 (느리지만 훔칠 때 새가 늦게 깸) |
| E / Space 꾹 | 둥지에서 알 훔치기 · 할당량 달성 후 굴에서 귀가 |
| P / Esc | 일시정지 |

모바일: 왼쪽 조이스틱 이동, `훔치기` 버튼(꾹), `살금` 토글.

## 규칙
- 알을 하나 훔칠 때마다 그 둥지 주인이 깨어나 추격합니다. 같은 둥지에서 더 훔치려면 주인을 멀리 유인한 뒤 돌아와야 해요.
- 따돌리는 방법: **덤불에 숨기**, **둥지에서 추격 거리 밖으로 도망**, **울타리 밖으로 나가기**. 포기한 새는 둥지로 돌아가 다시 잠듭니다.
- 잡히면 들고 있던 알은 원래 둥지로 돌아가고 목숨이 하나 줄어듭니다 (3개).
- 알은 최대 5개까지 들 수 있고, 하나 들 때마다 7%씩 느려집니다.
- 동이 트기 전까지 할당량을 채우면 다음 밤으로. 밤이 지날수록 빠른 새가 늘어납니다 (밤 2부터 칠면조·타조 등장).

## 추천 속도 (밸런스 표)
단위: 속도 = 칸/초, 회전 = 라디안/초. 값은 `index.html` 맨 위 `FOX`, `TYPES`에서 바로 고칠 수 있습니다.

| 캐릭터 | 속도 | 회전 | 깨는 시간 (살금살금) | 추격 거리 | 알 점수 | 알 개수 |
|---|---|---|---|---|---|---|
| 🦊 여우 (나) | 6.4 (살금 3.0) | 즉시 | - | - | - | 최대 5개 들기 |
| 🐔 암탉 | 4.2 | 8.0 | 0.45초 (1.0초) | 10칸 | 1점 | 3 |
| 🪿 거위 | 5.6 | 6.0 | 0.3초 (0.8초) | 15칸 | 2점 | 2 |
| 🦃 칠면조 | 6.2 | 4.5 | 0.6초 (1.3초) | 14칸 | 3점 | 2 |
| 🐦 타조 | 7.6 | 2.2 | 0.8초 (1.6초) | 22칸 | 6점 (황금알) | 1 |

여우가 알을 든 개수별 속도: 0개 6.4 · 1개 5.95 · 2개 5.5 · 3개 5.06 · 4개 4.61 · 5개 4.16

설계 의도:
- **암탉**: 알 1개만 든 여우보다 확실히 느림 → 입문용.
- **거위**: 가장 빨리 깸. 알 2개까지는 여우가 더 빠르지만, 3개부터는 따라잡힘.
- **칠면조**: 알을 든 여우보다 빠름. 대신 회전이 느려서 장애물을 끼고 돌거나 덤불로 따돌려야 함.
- **타조**: 직선에서는 절대 못 이김 (7.6). 회전이 매우 느려(2.2) 급하게 꺾으면 크게 헛돌기 때문에 지그재그로 피해야 함. 황금알 6점.
- 새는 목표 방향과 몸 방향이 어긋날수록 감속하므로, 회전 값이 곧 "따돌리기 쉬운 정도"입니다.

## 🏆 순위표 켜기 (모두가 보는 순위)
GitHub Pages에는 서버가 없어서, 무료 [Supabase](https://supabase.com) 데이터베이스에 점수를 저장합니다.

1. supabase.com 가입 → **New project** 생성
2. 왼쪽 **SQL Editor**에 아래를 붙여넣고 **Run**
   ```sql
   create table public.scores (
     id bigint generated always as identity primary key,
     nick text not null check (char_length(nick) between 1 and 12),
     score int not null check (score between 1 and 100000),
     night int not null check (night between 1 and 100),
     created_at timestamptz not null default now()
   );
   alter table public.scores enable row level security;
   create policy "anyone can read" on public.scores for select using (true);
   create policy "anyone can add" on public.scores for insert with check (true);
   grant select, insert on public.scores to anon;
   ```
3. **Project Settings → API**에서 `Project URL`과 `anon public` 키를 복사
4. `index.html`의 `SUPABASE_URL`, `SUPABASE_ANON_KEY` 두 줄에 붙여넣고 커밋

- anon 키는 웹페이지에 공개해도 되는 키입니다. (위 설정으로는 점수 읽기·추가만 가능, 수정·삭제 불가)
- 게임이 끝날 때마다 점수가 기록되고, 같은 닉네임은 최고 점수 하나만 순위에 보입니다.
- 이상한 기록은 Supabase **Table Editor → scores**에서 지우면 됩니다.
