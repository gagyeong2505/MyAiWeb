# 새 컴퓨터에서 MyAiWeb 저장소 연결 가이드

이 문서는 MyAiWeb 프로젝트가 없는 새 컴퓨터에서 GitHub 저장소를 클론하고 개발 환경을 구성하는 방법을 설명합니다.

## 사용자 요청 예시
```
새 컴퓨터에서 MyAiWeb 저장소 연결해줘
```

## AI가 수행해야 하는 작업 순서

### 1. 필수 프로그램 설치 확인

```powershell
# Git 설치 확인
git --version

# 설치 안 되어 있으면
winget install --id Git.Git

# GitHub CLI 설치 확인
gh --version

# 설치 안 되어 있으면
winget install --id GitHub.cli

# Node.js 설치 확인 (MCP 서버용)
node --version

# 설치 안 되어 있으면
winget install --id OpenJS.NodeJS.LTS
```

**주의**: 설치 후 터미널을 재시작해야 PATH에 반영됩니다.

### 2. GitHub CLI 로그인

```powershell
# GitHub 로그인 (브라우저 인증 방식)
gh auth login --web -p https
```

**로그인 과정:**
1. 터미널에 표시되는 일회용 코드를 복사
2. 자동으로 열리는 브라우저에서 https://github.com/login/device 접속
3. 일회용 코드 입력 후 GitHub 계정으로 인증

```powershell
# Git에 GitHub CLI 인증 연결
gh auth setup-git

# 로그인 상태 확인
gh auth status
```

**정상 출력 예시:**
```
github.com
  ✓ Logged in to github.com account gagyeong2505 (keyring)
  - Active account: true
  - Git operations protocol: https
  - Token scopes: 'gist', 'read:org', 'repo'
```

### 3. 저장소 클론

```powershell
# 원하는 위치로 이동 (예: C:\ 루트)
cd C:\

# 저장소 클론
gh repo clone gagyeong2505/MyAiWeb
```

**결과:** `C:\MyAiWeb` 폴더에 모든 파일이 다운로드됩니다.

### 4. 클론 결과 확인

```powershell
cd C:\MyAiWeb

# Git 상태 확인
git status

# 원격 저장소 연결 확인
git remote -v
```

**정상 출력 예시:**
```
origin  https://github.com/gagyeong2505/MyAiWeb.git (fetch)
origin  https://github.com/gagyeong2505/MyAiWeb.git (push)
```

### 5. 로컬 설정 파일 복원

`.gitignore`로 제외된 민감한 파일들을 수동으로 생성해야 합니다.

#### 5-1. .mcp.json 생성 (프로젝트 루트)

```json
{
  "mcpServers": {
    "git": {
      "type": "stdio",
      "command": "npx",
      "args": ["@cyanheads/git-mcp-server@latest"],
      "env": {
        "GIT_USERNAME": "gagyeong2505",
        "GIT_EMAIL": "gagyeong2505@naver.com",
        "GITHUB_TOKEN": "여기에_토큰_입력"
      }
    }
  }
}
```

#### 5-2. GitHub 토큰 확인 방법

```powershell
# 현재 로그인된 토큰 확인
gh auth token
```

위 명령어의 출력값을 `.mcp.json`의 `GITHUB_TOKEN`에 입력합니다.

### 6. Claude Code 설정 확인

```powershell
# Claude Code에서 MCP 서버 연결 확인
# /mcp 명령어로 git 서버가 connected 상태인지 확인

# GitHub CLI 경로 확인 (PATH 문제 시)
where gh

# PATH에 없는 경우 전체 경로 사용
"C:\Program Files\GitHub CLI\gh.exe" --version
```

### 7. 최종 동작 테스트

```powershell
# Git 상태 확인
git status

# 최근 커밋 이력 확인
git log --oneline -5

# GitHub CLI 인증 확인
gh auth status
```

---

## 완료 후 체크리스트

| 항목 | 확인 명령어 | 기대 결과 |
|------|------------|-----------|
| Git 설치 | `git --version` | 버전 번호 출력 |
| GitHub CLI 설치 | `gh --version` | 버전 번호 출력 |
| Node.js 설치 | `node --version` | 버전 번호 출력 |
| GitHub 로그인 | `gh auth status` | ✓ Logged in |
| 저장소 클론 | `git remote -v` | origin URL 표시 |
| MCP 설정 | `.mcp.json` 파일 존재 | 토큰 포함 확인 |

---

## 문제 해결

### 1. gh 명령어를 찾을 수 없는 경우
- 터미널 재시작 (PATH 갱신)
- 전체 경로로 실행: `"C:\Program Files\GitHub CLI\gh.exe"`
- 환경 변수에 수동 추가: `C:\Program Files\GitHub CLI`

### 2. git push 시 권한 오류 (다른 계정으로 인증됨)
```powershell
# GitHub CLI 인증을 Git에 연결
gh auth setup-git
```

### 3. 클론 시 권한 오류
```powershell
# 인증 상태 재확인
gh auth status

# 재로그인
gh auth login --web -p https
```

### 4. MCP 서버 연결 실패
```powershell
# Node.js 설치 확인
node --version

# npx 동작 확인
npx --version

# .mcp.json 파일 위치가 프로젝트 루트(C:\MyAiWeb)인지 확인
```

---

## 전체 프로세스 요약

```
새 컴퓨터
  ├── 1. 필수 프로그램 설치 (Git, gh, Node.js)
  ├── 2. gh auth login (GitHub 브라우저 인증)
  ├── 3. gh auth setup-git (Git 인증 연결)
  ├── 4. gh repo clone gagyeong2505/MyAiWeb (코드 다운로드)
  ├── 5. .mcp.json 수동 생성 (토큰 포함이라 Git에 없음)
  └── 6. git status + gh auth status (최종 확인)
```
