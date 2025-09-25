# CLAUDE.md

이 파일은 Claude Code(claude.ai/code)가 이 저장소에서 작업할 때 필요한 가이드를 제공합니다.

## 프로젝트 개요

이 프로젝트는 **카카오톡 OAuth 인증 테스트**와 **구글 워크스페이스 문서 리디렉션 시스템**을 위한 웹 애플리케이션입니다. Vercel에 배포되어 있으며(https://kakao-test-ebon.vercel.app), 데이터 저장을 위해 구글 앱스 스크립트와 연동됩니다.

## 아키텍처

### 기술 스택
- **프론트엔드**: 순수 HTML5, CSS3, Vanilla JavaScript (빌드 과정 불필요)
- **인증**: 카카오톡 OAuth 2.0 API
- **백엔드 연동**: 구글 앱스 스크립트 웹앱
- **호스팅**: Vercel (URL 리라이트 규칙 적용)
- **모바일 지원**: 안드로이드/iOS 딥링크

### 핵심 구성요소

1. **카카오톡 OAuth 시스템**: 서로 다른 클라이언트 ID로 여러 인증 플로우 지원
   - 기본 로그인 (`index.html`)
   - 친구별 인증 (`oauth.html`)
   - 친구 목록 API 테스트 (`kakao_friends_test.html`)

2. **문서 리디렉션 시스템** (`go.html`): 크로스 플랫폼 문서 접근
   - 8가지 문서 유형 지원 (구글 시트, 사이트, 폼, 캘린더)
   - 플랫폼별 딥링크 (안드로이드 Intent URL, iOS 커스텀 스킴)
   - 데스크톱용 웹 버전 폴백

3. **구글 앱스 스크립트 연동**:
   - 스프레드시트에 토큰 저장
   - API 엔드포인트: `https://script.google.com/macros/s/[SCRIPT_ID]/exec`

## 개발 명령어

정적 웹 애플리케이션이므로 빌드 명령어가 필요하지 않습니다:

```bash
# 로컬 개발 - 정적 파일 서빙
npx serve public/

# Vercel 배포 (git push 시 자동 배포)
vercel --prod
```

## 주요 설정

### Vercel 배포 설정 (`vercel.json`)
- URL 리라이트로 깔끔한 URL과 라우팅 처리
- 모든 라우트는 `/public` 디렉토리에서 서빙

### 카카오톡 API 설정
- **기본 인증 클라이언트 ID**: `281c7fd497f8333d209a0832cdf43cdb`
- **친구 인증 클라이언트 ID**: `b753d319ccd4300a4c957d7d4c6c9b96`
- 필요한 권한: `talk_message`, `friends`

### 리디렉션 지원 문서 유형
- 발주확인페이지, 구매페이지, 구매캘린더, 대시보드, 카카오토큰발급, 공구관리점검표, 온습도점검표, 생산메인화면

## 모바일 딥링크 구현

플랫폼별 딥링크를 구현했습니다:

- **안드로이드**: 구글 앱용 Intent URL (`intent://` 스킴) 사용
- **iOS**: 커스텀 스킴 (`googlesheets://`, `googlecalendar://`) 사용
- **데스크톱**: 웹 버전으로 폴백

## OAuth 플로우 아키텍처

1. 사용자가 친구 선택 → 카카오톡 로그인 → 토큰 교환
2. 구글 앱스 스크립트를 통해 토큰 저장 → 저장된 토큰으로 API 호출
3. 친구 선택 시스템은 사전 정의된 9명의 사용자 지원

## 중요한 파일 구조

```
public/
├── index.html              # 메인 카카오톡 로그인
├── oauth.html              # 친구별 인증
├── kakao_friends_test.html # 친구 목록 API 테스트
├── go.html                 # 문서 리디렉션 시스템
└── auth/kakao/callback.html # OAuth 콜백 핸들러
```

## 보안 고려사항

- 클라이언트 ID는 프론트엔드 코드에 노출됨 (OAuth 공개 클라이언트 표준)
- 토큰은 클라이언트 사이드에서 처리되고 구글 앱스 스크립트를 통해 저장됨
- 카카오톡 API 호출 시 CORS 정책을 주의깊게 처리해야 함