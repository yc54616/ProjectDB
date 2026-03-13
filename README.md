# ASC Project DB

ASC 보안 동아리 프로젝트 보고서 관리 시스템

쿼드 팀이 Git PR로 격주 보고서를 제출하면, 관리자 리뷰 후 merge 시 자동으로 Notion DB에 동기화됩니다.

## 구조

```
reports/
└── {YYYY}/
    └── {쿼드조명}/
        └── {프로젝트명}/
            ├── report-01.md    # 격주 보고서
            ├── report-02.md
            ├── ...
            └── assets/         # 스크린샷, 다이어그램 (선택)
```

## 빠른 시작

### 1. 레포 Fork & Clone

```bash
# 본인 GitHub 계정으로 Fork 후
git clone https://github.com/<your-username>/ProjectDB.git
cd ProjectDB
```

### 2. 보고서 작성

```bash
# 디렉토리 생성
mkdir -p reports/2026/A조/web-scanner

# 템플릿 복사
cp templates/report-template.md reports/2026/A조/web-scanner/report-01.md

# 보고서 작성
```

### 3. 로컬 검증

```bash
pip install -r scripts/requirements.txt
python scripts/validate_frontmatter.py
```

### 4. PR 제출

```bash
git checkout -b project/A조/web-scanner/report-01
git add .
git commit -m "Add report: A조/web-scanner report-01"
git push origin project/A조/web-scanner/report-01
# GitHub에서 PR 생성
```

자세한 제출 방법은 [CONTRIBUTING.md](CONTRIBUTING.md)를 참고하세요.

## 파이프라인

```
[쿼드 팀] -> fork/branch -> [PR 제출] -> 리뷰 -> [merge] -> [GitHub Actions] -> [Notion DB]
                                 |                              |
                           CI: frontmatter 검증         변경된 .md 파싱 -> 동기화
```

## 설정 (관리자)

### GitHub Secrets

| Secret | 설명 |
|--------|------|
| `NOTION_API_KEY` | Notion Internal Integration Token |
| `NOTION_PROJECT_DB_ID` | 대상 Notion ProjectDB ID |

### Notion DB 스키마

| 속성명 | 타입 | 비고 |
|--------|------|------|
| 프로젝트명 | Title | PK 역할 |
| 쿼드 조 | Select | A조, B조, ... |
| 조원 | Rich text | members join |
| 보고 회차 | Number | 1~8 |
| 제출일 | Date | |
| 진행 상태 | Status | 시작 전/진행 중/보류/완료 |
| CL 등급 | Select | CL1~CL4 |
| 최종 보고서 | Checkbox | is_final |
| 프로젝트 유형 | Select | 운영진 수동 판정 (기초/심화) |
| Git 링크 | URL | 보고서 원문 링크 |
| 기여도 | Rich text | 팀원별 기여도 |

### 브랜치 보호 규칙 (권장)

- `main` 브랜치 직접 push 금지
- PR 필수, CI 통과 필수
- 최소 1명 리뷰 승인 필수
