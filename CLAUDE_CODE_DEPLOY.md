# OASEED 사이트 배포 지시문 (Claude Code · Mac) — 2026-10-19

## 0. 전제 (FACT · 10/19 20:50 ICT RDAP 조회)
| 도메인 | 상태 | 비고 |
|---|---|---|
| **oaseed.kr** | KISA RDAP "Not Found" → **등록 가능으로 판단** (최종 확인은 등록기관 검색) | 1순위 |
| oaseed.co.kr | 미조회(정보 부족) | 2순위 |
| oaseed.com | **이미 등록됨** (2025-06-25, 만료 2027-06-25, Tucows, Google Cloud DNS) — 현재 일본 아로마캔들 브랜드 「オアシード」 Shopify 몰 운영 중 | 확보 불가. 상표 충돌은 아님(캔들 ≠ 식품) |
| oaseed.net / .shop / .co | RDAP 404 → 등록 가능으로 판단 | 보조 |

## 1. 도메인 등록 (대표님 직접 · 결제)
가비아 또는 후이즈에서 `oaseed.kr` 검색 → 등록인 주식회사 오엔엘 / 696-86-00595 / 충남 아산시 염치읍 현충사길93번길 1 / onllkorea@gmail.com / 2년 / 자동연장 ON.
등록 직후 **네임서버를 Cloudflare로 이관**하는 것이 가장 간단하다(아래 2-③에서 Cloudflare가 NS 2개를 알려줌 → 가비아 "네임서버 설정"에 입력).

## 2. Cloudflare Pages 배포 (Claude Code 실행 · 결제 없음)
```bash
# ① 준비 — 이 폴더(deploy/)가 사이트 루트다
cd ~/Documents/OASEED_website/deploy
ls   # index.html  assets/  _headers  _redirects  robots.txt  sitemap.xml

# ② wrangler 설치·로그인 (Cloudflare 무료 계정, 브라우저 로그인 1회)
npm i -g wrangler
wrangler login

# ③ 프로젝트 생성 + 배포  → https://oaseed.pages.dev 즉시 생성
wrangler pages project create oaseed --production-branch main
wrangler pages deploy . --project-name oaseed --branch main

# ④ 커스텀 도메인 (Cloudflare 대시보드 → Pages → oaseed → Custom domains)
#    oaseed.kr 과 www.oaseed.kr 추가 → 네임서버 이관 시 DNS 자동 생성, HTTPS 자동
```

## 3. 도메인 연결 후 (Claude Code)
```bash
# 허브 QR을 도메인 기준으로 재생성 + 재배포
cd ~/Documents/OASEED_website
python3 build3.py "https://oaseed.kr"          # index.html 재빌드(허브 QR = oaseed.kr)
python3 make_deploy.py                          # deploy/ 갱신 (상세페이지 외부파일 분리·OG·헤더)
cd deploy && wrangler pages deploy . --project-name oaseed --branch main
# 인쇄용 허브 QR 재발급
python3 - <<'EOF'
import qrcode; q=qrcode.QRCode(error_correction=qrcode.constants.ERROR_CORRECT_H,box_size=24,border=2); q.add_data('https://oaseed.kr'); q.make()
q.make_image(fill_color='#1E1F1F',back_color='white').save('QR/OASEED_QR_hub_oaseed_kr.png'); print('ok')
EOF
```
- 기존 아티팩트 URL QR(`QR/OASEED_QR_hub_site.png`)은 폐기.
- 인스타그램 bio 링크 → `https://oaseed.kr`. 스마트스토어·쿠팡 상세페이지 하단에 `oaseed.kr` 표기.

## 4. 짧은 주소 (이미 _redirects 에 포함)
`oaseed.kr/ig` → 인스타그램 · `oaseed.kr/shop` → 스마트스토어 · `oaseed.kr/coupang-apple` · `oaseed.kr/coupang-pear`
→ 인쇄물·박람회 명함에 QR 대신 글자로 쓸 수 있다.

## 5. 체크리스트
- [ ] oaseed.kr 등록 완료(등록인 = 법인)
- [ ] `wrangler pages deploy` 성공 → oaseed.pages.dev 열림
- [ ] 네임서버 Cloudflare 이관 → oaseed.kr HTTPS 열림(전파 최대 24h, 보통 10분)
- [ ] build3.py "https://oaseed.kr" 재빌드 → 허브 QR 교체 → 재배포
- [ ] IG bio 링크 교체
- [ ] (선택) oaseed.net 방어 등록
