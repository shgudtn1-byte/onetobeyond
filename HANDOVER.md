# ONE TO BEYOND 기도편지 사이트 — 인계 문서

손원규가 만든 기도편지 사이트(https://onetobeyond.org)를 목사님께 넘겨드리기 위한 문서입니다.
목사님과, 목사님이 쓰시는 AI(Claude 등)가 함께 읽도록 썼습니다.

---

## 1. 먼저 알아두실 점: 글 수정에는 코드가 필요 없습니다

새 기도편지 쓰기, 사진 올리기, 기도제목 수정, 주소록, 발송은 모두 **관리자 화면**에서 합니다.

- 주소: https://onetobeyond.org/admin
- 접속 코드: 원규에게 따로 받으세요 (이 문서에는 적지 않습니다). 받은 뒤 관리자 화면 **설정 → 접속 코드 바꾸기**에서 꼭 바꿔 주세요.
- 휴대폰에서도 편집할 수 있고, ‘임시 저장’한 초안은 컴퓨터에서 이어서 쓸 수 있습니다.
- ‘공개 반영’을 눌러야 방문자 화면이 바뀝니다.

**코드(디자인·기능)를 바꿀 때만** 아래 3~6번이 필요합니다.

---

## 2. 사이트가 어디에 있나 (계정 지도)

| 무엇 | 서비스 | 현재 소유 계정 | 하는 일 |
|---|---|---|---|
| 도메인 onetobeyond.org | Squarespace Domains | 원규 | 주소. DNS: A `@` → `75.2.60.5`, CNAME `www` → `moonlit-fox-8252c4.netlify.app` |
| 웹사이트 파일 | Netlify (프로젝트 `moonlit-fox-8252c4`) | 원규 | `site/` 폴더를 그대로 올려 보여줌. HTTPS 자동 |
| 글·사진·주소록·댓글 DB | Supabase (프로젝트 `tmsvplpommtdxvhyqusw`) | 원규 | 데이터, 관리자 로그인, 사진 저장 |
| 댓글용 Google 로그인 | Google Cloud (프로젝트 `just-cosmos-514207-c6`) | 원규 (wonkyushon@gmail.com) | ‘마음 나눔’ Google 로그인 |
| 소스 코드 | GitHub `wonshon-cmd/Onebybeyond` (브랜치 `claude/onebybeyond-setup-ajqrj5`) | 원규 | 코드 보관. 배포와는 연결되어 있지 않음 |

---

## 3. 넘겨받는 방법 (계정 이전)

비밀번호를 주고받지 말고, 각 서비스의 **초대/이전 기능**을 쓰세요. 목사님이 먼저 각 서비스에 본인 계정으로 가입해 두시면 됩니다.

1. **Netlify**: 원규가 Netlify → 프로젝트 → **Project configuration → General → Transfer project**로 목사님 팀에 이전
   (또는 Team settings → Members에서 목사님을 Owner로 초대)
2. **Supabase**: 원규가 **Organization settings → Team → Invite**로 목사님을 Owner로 초대
   (또는 Project settings → General → **Transfer project**로 목사님 조직에 이전)
3. **Google Cloud**: 원규가 **IAM & Admin → IAM → Grant access**로 목사님 Google 계정을 **Owner**로 추가
4. **Squarespace 도메인**: 원규가 Domains → onetobeyond.org → **Permissions**에서 목사님을 추가하거나, 도메인을 목사님 Squarespace 계정으로 이전
5. **GitHub**: 원규가 저장소 Settings → Collaborators에 목사님 추가, 또는 **Transfer ownership**
6. 이전이 끝나면 목사님이:
   - 관리자 **접속 코드 변경** (관리자 화면 → 설정)
   - 관리자 이메일 목록 정리 (관리자 화면 → 설정 → 관리자 이메일): 목사님 Google 이메일 추가, 필요 없는 주소 삭제

---

## 4. 코드 구조 (AI가 읽을 부분)

순수 HTML/CSS/JS 정적 사이트입니다. 빌드 도구·프레임워크·npm 의존성 없음.

```
site/                       ← Netlify에 그대로 올리는 폴더 (배포 단위)
  index.html                방문자 페이지 뼈대 (CSP 메타 포함)
  app.js                    방문자 화면: 소식지 로딩·렌더, 하트/아멘(브라우저 localStorage), 공유, 구독 신청
  render.js                 소식지 HTML 렌더 + 서식 허용목록 sanitizer + YouTube ID 파싱 (방문자·관리자 미리보기 공용)
  sb.js                     Supabase 최소 클라이언트 (Auth/REST/Storage/Functions를 fetch로 직접 호출, SDK 없음)
  community.js              ‘마음 나눔’: Google 로그인(Supabase OAuth, implicit flow), 공개/비공개 글
  config.js                 Supabase URL + publishable key (공개용 값), 관리자 로그인용 이메일
  content.json              Supabase 연결 실패 시 보여줄 기본 소식지 (첫 호)
  style.css                 승인된 디자인(Off-white Editorial Letter) — 원본 유지
  site.css                  추가 스타일
  admin/index.html, admin.js, admin.css   관리자 화면 (해시 라우팅: #letters, #edit/<id>, #contacts, #send, #posts, #settings)
  _headers                  Netlify 보안 헤더
supabase/
  schema.sql                모든 표 + RLS 정책 + 함수(is_admin, subscribe) + 사진 버킷 정책. 다시 실행해도 안전
  seed.sql                  첫 번째 소식지 데이터
  functions/send-newsletter/index.ts   이메일 발송 Edge Function (Resend 사용) — 아직 배포 안 됨
demo/
  build.py, demo-backend.js 인터넷 없이 열리는 한 파일짜리 데모 생성기 (python3 demo/build.py out.html)
design-package/             처음 받은 디자인 인계 원본 (참고용, 배포 안 함)
```

**데이터 모델** (Supabase `public` 스키마)
- `letter_drafts(id, data jsonb, updated_at)` — 관리자 초안 (관리자만)
- `letters_published(id, data jsonb, published_at)` — 공개본 (누구나 읽기)
- `site_settings(key, value)` — `order`(소식지 순서 배열), `featured`(첫 화면 소식지 id)
- `posts` — 마음 나눔 글. `visibility` = public/private. 비공개는 작성자와 관리자만 (RLS)
- `contacts`, `contact_groups`, `sends` — 주소록·그룹·발송 기록 (관리자만)
- `admins(email)` — 관리자 이메일. `is_admin()`이 JWT 이메일로 확인
- Storage 버킷 `media` — 사진 (공개 읽기, 관리자만 업로드)

소식지 `data` 모양:
`{ id, issue, date, title, subtitle, cover, sidePhoto, intro(html), quote, sections:[{title, html, photos:[{url,caption}]}], prayers:[{title, body}], gallery:[{url, caption}], youtube:{url, title, description} }`

**관리자 로그인 방식**: Supabase Auth의 `admin@onetobeyond.org` 사용자 비밀번호 = 접속 코드. 모든 쓰기는 DB의 RLS(`is_admin()`)가 다시 검사합니다.

**지켜야 할 디자인 규칙** (`design-package/DESIGN-SYSTEM.md`)
- 오프화이트 Editorial Letter 디자인 유지 (배경 #FAF9F6, 본문 #343733, 포인트 #8B7D6B)
- 로고: 오렌지 #FF7900 / 그린 #009E60 사선 두 개 + ONE TO BEYOND
- 이야기 끝에는 하트만, 기도제목은 ✧ 아멘 ↔ ✓ 함께 기도했어요
- 후원·결제 기능 넣지 않기

---

## 5. 코드 수정 후 배포하는 법

1. `site/` 안의 파일을 고칩니다.
2. 확인: `cd site && python3 -m http.server 8000` → http://localhost:8000 , http://localhost:8000/admin/
3. 배포: Netlify → 프로젝트 → **Deploys** 화면 아래 칸에 `site` 폴더를 끌어다 놓기
   (원하면 Netlify를 GitHub 저장소와 연결해 push할 때 자동 배포되게 바꿀 수 있습니다. Publish directory = `site`)
4. DB 구조를 바꿨다면 Supabase SQL Editor에서 실행

---

## 6. 아직 남은 일

| 항목 | 상태 | 필요한 것 |
|---|---|---|
| 마음 나눔 Google 로그인 | Google Cloud 클라이언트 생성 완료. Supabase Google provider 설정 확인 필요 | Supabase → Auth → Providers → Google에 Client ID / Secret 정확히 입력, Google Cloud에서 **Publish app** |
| 이메일 발송 | 코드 있음, 미배포 | Resend 가입·도메인 인증(Squarespace DNS 추가, Email Security 레코드 수정), `send-newsletter` 함수 배포, 시크릿 `RESEND_API_KEY`·`MAIL_FROM` 설정 (README 참고) |
| 카카오톡 | 휴대폰 공유 창으로 직접 보내기만 됨 | 자동 일괄 발송은 카카오 비즈메시지 계약 필요 |
| 하트·아멘 숫자 | 방문자 각자 브라우저에만 기억 | 모두 함께 보는 숫자가 필요하면 Supabase 표 추가 |

---

## 7. 비밀 정보 다루기

- 이 문서와 코드에는 비밀번호·비밀키가 없습니다. `config.js`의 Supabase **publishable key**는 공개용이라 괜찮습니다.
- 관리자 **접속 코드**, Google **Client secret**, Supabase **secret / service_role 키**는 채팅·메일·문서에 적지 말고, 받은 즉시 새로 바꾸세요.
