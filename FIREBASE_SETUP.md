# Firebase Web Push 설정

## 1. Firebase 프로젝트
- Firebase Console에서 프로젝트를 만든다.
- Authentication > Sign-in method에서 Anonymous를 활성화한다.
- Firestore Database를 만든다.
- Project settings > General > Your apps에서 Web 앱을 추가한다.
- 받은 Firebase config를 `index.html`의 `FIREBASE_CONFIG`에 넣는다.
- Project settings > Cloud Messaging > Web Push certificates에서 VAPID key pair를 생성한다.
- 공개 VAPID key를 `index.html`의 `FIREBASE_VAPID_KEY`에 넣는다.
- 같은 Firebase config를 `service-worker.js`의 `FIREBASE_CONFIG`에도 넣는다.

## 2. 웹 파일
GitHub Pages 루트에 다음을 업로드한다.
- index.html
- manifest.json
- service-worker.js
- icons/

## 3. Firebase 백엔드
이 `firebase` 폴더는 GitHub Pages 루트에 넣는 폴더가 아니라 Firebase Functions 배포용이다.

Node.js 20+와 Firebase CLI를 설치한 뒤 `firebase` 폴더에서 functions 패키지를 설치하고 Firebase 프로젝트에 연결한다.

예시:
```bash
npm install -g firebase-tools
firebase login
cd firebase
firebase use --add
cd functions
npm install
cd ..
firebase deploy --only functions,firestore:rules
```

Cloud Functions의 예약 함수는 Cloud Scheduler를 사용한다. 이 프로젝트는 매 1분마다 만료된 타이머를 확인한다.

## 4. 주의
예약 함수 배포에는 Firebase의 Blaze(종량제) 플랜이 필요할 수 있다. 실제 사용량이 적더라도 Google Cloud/Firebase의 현재 요금과 무료 할당량을 확인한다.

## 5. 동작
- 사용자가 PWA에서 알림 허용
- 25분 공부 시작
- 브라우저가 FCM 토큰을 Firebase에 등록
- Firestore에 종료 시각이 저장됨
- Cloud Function이 종료 시각을 확인
- FCM으로 iPad PWA에 data 메시지를 전송
- service-worker.js가 백그라운드에서 `showNotification()` 실행

공부 기록 자체는 기존처럼 브라우저 localStorage에 저장된다. Firebase에는 푸시 예약에 필요한 익명 사용자 ID, FCM 토큰, 종료 시각 등이 저장된다.
