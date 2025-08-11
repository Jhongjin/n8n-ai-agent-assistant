# n8n AI Agent Assistant - Google Calendar 일정 관리 자동화

Google Calendar와 연동된 AI 어시스턴트로 일정 관리와 이메일 리마인더를 자동화하는 n8n 워크플로우입니다.

## 🚀 주요 기능

• **지능형 일정 관리**: Google Calendar와 연동하여 일정 조회, 생성, 수정, 삭제
• **자동 이메일 리마인더**: 새로운 일정 추가 시 자동 알림 메일 발송
• **스마트 연락처 관리**: Google Sheets를 통한 팀원 연락처 자동 조회
• **대화형 인터페이스**: 자연어로 일정 관리 요청 처리
• **메모리 기능**: 대화 히스토리 기반의 컨텍스트 유지

## 🏗️ 워크플로우 구조

### 노드 구성

#### 1. **Chat Trigger**
   • 사용자의 채팅 메시지를 받아 워크플로우 시작
   • Webhook ID를 통한 외부 연동 지원

#### 2. **AI Agent** (핵심)
   • Google Gemini 모델 기반의 지능형 어시스턴트
   • 한국어 시스템 프롬프트로 자연스러운 대화
   • 5개의 전용 도구와 연동하여 작업 수행

#### 3. **Simple Memory**
   • 최근 10개 대화 내용 저장
   • 컨텍스트 기반의 연속적인 대화 지원

### 연동 도구들

#### 📅 **Get Schedule**
   • Google Calendar API 연동
   • 특정 날짜/기간의 일정 조회
   • 일정 존재 여부 확인

#### ➕ **Create Event** 
   • 새로운 캘린더 일정 생성
   • 참석자, 설명 등 상세 정보 포함
   • 자동 알림 설정 지원

#### 🗑️ **Delete Event**
   • 기존 일정 삭제
   • Event ID 기반 정확한 식별
   • 일정 수정 시 기존 일정 제거용

#### 👥 **Get Contacts**
   • Google Sheets 연동
   • 팀원 이메일 주소 자동 조회
   • 일정 참석자 자동 매칭

#### 📧 **Send Email**
   • Gmail API 연동
   • 일정 리마인더 자동 발송
   • 사용자 동의 후에만 발송

## 🤖 AI 어시스턴트 동작 원리

### 일정 처리 프로세스

1. **연락처 확인**
   ```
   Get Contacts 도구로 Google Sheets에서 팀원 이메일 조회
   ```

2. **기존 일정 조회**
   ```
   Get Schedule 도구로 요청 날짜의 캘린더 확인
   ```

3. **일정 생성/수정**
   ```
   신규: Create Event로 새 일정 생성
   수정: Delete Event → Create Event 순서로 진행
   ```

4. **리마인더 확인**
   ```
   사용자에게 "리마인더 이메일을 보내드릴까요?" 질문
   ```

5. **이메일 발송**
   ```
   승인 시: Send Email 도구로 알림 메일 자동 발송
   ```

### 스마트 일정 관리

• **중복 방지**: 기존 일정 자동 확인
• **충돌 해결**: 일정 변경 시 기존 일정 삭제 후 재생성  
• **참석자 매칭**: 시트 데이터 기반 자동 이메일 할당
• **컨텍스트 유지**: 이전 대화 내용 기반 지능형 응답

## 📊 시스템 프롬프트 분석

### 핵심 역할 정의
```
🔹 1. 일정 처리 (Google Calendar)
- 도구 사용 순서: Get Contacts → Get Schedule → Create/Delete Event
- 일정 변경 시 기존 삭제 후 재생성 원칙

🔹 2. 리마인더 이메일 처리  
- 새 일정 추가/수정 시 사용자 동의 확인
- 긍정적 응답 시에만 Send Email 도구 실행
```

### 중요 원칙
• **사용자 동의 우선**: 임의 이메일 발송 금지
• **정확한 작업 순서**: Delete → Create 패턴 준수
• **필요 작업만 수행**: 과도한 자동화 방지

## 🔧 설정 및 연동

### 필요한 계정 및 권한

#### Google Calendar API
```
캘린더 ID: 7d37a3890191d5eb9e416f5b39c7fd4486d6ac49850cad052dbb55db6289e40f@group.calendar.google.com
권한: 일정 조회, 생성, 수정, 삭제
```

#### Google Sheets API  
```
시트 ID: 1o9XElTr-Mhl4GYh7Cc5VKnE_TeLsI7z58U_v2Vild8k
시트명: 시트1 (gid=0)
권한: 읽기 전용
```

#### Gmail API
```
기능: 메일 발송
권한: 발송 전용
```

#### Google Gemini API
```
모델: Google Gemini Chat Model
용도: AI 어시스턴트 언어 모델
```

### 환경 설정

#### 타임존
```
Asia/Seoul (한국 시간 기준)
```

#### 실행 정책
```
실행 순서: v1
호출 정책: 같은 소유자의 워크플로우에서만
실행 제한: 무제한
```

## 📝 사용 예시

### 일정 생성
```
사용자: "내일 오후 2시에 팀 미팅 일정 잡아줘"
AI: 
1. 팀원 이메일 조회 (Get Contacts)
2. 내일 일정 확인 (Get Schedule)  
3. 새 일정 생성 (Create Event)
4. "리마인더 이메일을 보내드릴까요?"
```

### 일정 수정  
```
사용자: "아니다, 3시로 변경해줘"
AI:
1. 기존 일정 삭제 (Delete Event)
2. 새로운 시간으로 일정 재생성 (Create Event)
3. "리마인더 이메일을 보내드릴까요?"
```

## 🔍 워크플로우 JSON 구조

### 주요 노드 ID 매핑
```
Chat Trigger: 605388bd-2dcb-45e2-8832-ae4117aeb714
AI Agent: f3300529-4e27-4b23-8bf0-b0e16db572c2
Memory: 1d94ef01-2908-49f1-a4f3-7b48a0966da6
Get Schedule: 69e8a79a-4e17-4ed0-aad9-94db2ca3f67a
Create Event: 3e3a9292-5922-47a8-8580-d42afa590626
Delete Event: d6e8cba1-d786-45cf-86e1-6e13464a5054
Get Contacts: 6deb5cf8-bfbc-4746-890c-7c944dd42a1e
Send Email: 2a0ae886-5f38-4f74-9746-bfad84fda271
```

## 🚦 상태 및 버전 정보

• **활성 상태**: 비활성 (설정 완료 후 활성화 필요)
• **버전 ID**: fca1a435-10fc-40d9-b325-cd269bed77a4
• **워크플로우 ID**: w34EVrZ9himafQUW
• **인스턴스 ID**: ff51bf6df13280a7c18ba3c723b7ac1595646c1a96e99462da81d922e4fffd36

## 🔐 보안 및 권한

### API 자격증명
• Google Calendar OAuth2: ZIaGM7O1kAuiYgSF
• Google Sheets OAuth2: ehUFWrQ4rybIKqg8  
• Gmail OAuth2: ZRU0nd89wnY4zysO
• Google Gemini API: zyx0j91HDtpY8SzJ

### 보안 원칙
• OAuth2 기반 안전한 인증
• 최소 권한 원칙 적용
• 사용자 동의 기반 작업 수행

## 🛠️ 설치 및 실행

1. **n8n 환경 준비**
2. **API 계정 설정** (Google Calendar, Sheets, Gmail, Gemini)
3. **워크플로우 JSON 가져오기**
4. **자격증명 연결**
5. **테스트 실행**
6. **활성화**

## 📈 확장 가능성

• **다중 캘린더 지원**: 여러 캘린더 통합 관리
• **SMS 알림**: 이메일 외 SMS 리마인더 추가
• **회의실 예약**: 공간 자원 관리 연동
• **반복 일정**: 주기적 일정 자동 생성
• **참석자 관리**: RSVP 응답 처리

## 🤝 기여 및 문의

이 프로젝트에 대한 개선 사항이나 문의사항이 있으시면 Issue를 통해 연락해주세요.

---

**Made with ❤️ using n8n workflow automation**
