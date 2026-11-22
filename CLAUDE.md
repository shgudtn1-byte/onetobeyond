# One to Beyond — 작업 메모 (Claude용)

코트디부아르 아비장에서 보내는 기도편지 사이트 https://onetobeyond.org .
처음 만든 사람은 손원규이고, 2026-11-21에 인계받았어요. 전체 설명은 `HANDOVER.md`와 `README.md`를 먼저 읽으세요.

- 사용자에게는 항상 한국어로 답합니다.
- 정적 사이트(빌드 없음): `site/`가 배포 단위. Netlify 프로젝트 `moonlit-fox-8252c4`.
- 데이터: Supabase `tmsvplpommtdxvhyqusw` (스키마 `supabase/schema.sql`, RLS로 권한 검사).
- 디자인 규칙: `design-package/DESIGN-SYSTEM.md`를 지킵니다 (오프화이트 Editorial Letter, 후원·결제 기능 없음).
- 로컬 확인: `cd site && python3 -m http.server 8000`
- 글·사진·주소록 수정은 관리자 화면(/admin)에서 합니다. 코드는 디자인이나 기능을 바꿀 때만 고칩니다.
- 비밀값(접속 코드, service_role 키, Resend 키, Google client secret)은 절대 커밋하지 않습니다. `config.js`의 publishable key는 공개용이라 괜찮습니다.
- 인계 시점의 zip은 사이트에 올라간 버전보다 조금 더 새로웠습니다 (`render.js`의 data:image 허용, `community.js`의 Google 로그인 준비 확인). 다음 배포 때 이 변경도 함께 올라갑니다.
