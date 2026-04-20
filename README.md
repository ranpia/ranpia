# 👋 About Me
- 🎓 컴퓨터공학 졸업
- 💡 **Spring Boot 백엔드**와 **AI 프로젝트** 중심 개발 경험
- 🌱 AWS·GCP 기반 클라우드 배포 및 CI/CD 경험
- 🤝 팀 프로젝트 리딩 및 협업 경험 다수
- 📫 Email: richard.kim146@gmail.com  

---

## 📊 GitHub Stats
![GitHub stats](https://github-readme-stats.vercel.app/api?username=ranpia&show_icons=true&theme=blue_navy)
![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=ranpia&layout=compact&theme=blue_navy)

---

## 🛠 Tech Stack
**Backend**  
![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring%20Security-6DB33F?style=for-the-badge&logo=springsecurity&logoColor=white)
![Spring WebFlux](https://img.shields.io/badge/Spring%20WebFlux-6DB33F?style=for-the-badge&logo=spring&logoColor=white)
![JPA](https://img.shields.io/badge/Hibernate%20JPA-59666C?style=for-the-badge&logo=hibernate&logoColor=white)
![REST API](https://img.shields.io/badge/REST%20API-005571?style=for-the-badge&logo=postman&logoColor=white)

**Frontend**  
![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)
![JSP](https://img.shields.io/badge/JSP-007396?style=for-the-badge&logo=java&logoColor=white)

**AI / ML**  
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
![OpenAI API](https://img.shields.io/badge/OpenAI-412991?style=for-the-badge&logo=openai&logoColor=white)

**Database**  
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![Oracle](https://img.shields.io/badge/Oracle-F80000?style=for-the-badge&logo=oracle&logoColor=white)
![AWS RDS](https://img.shields.io/badge/AWS%20RDS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)

**Infra / DevOps**  
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![GCP](https://img.shields.io/badge/GCP-4285F4?style=for-the-badge&logo=google-cloud&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)

---

## 🚀 대표 프로젝트
> 제가 직접 설계·구현한 주요 프로젝트입니다.

### 🏛 MoleLaw — 임베딩 기반 RAG 법률 상담 서비스
![Java](https://img.shields.io/badge/Java%2017-ED8B00?style=flat&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot%203.5-6DB33F?style=flat&logo=springboot&logoColor=white)
![OpenAI API](https://img.shields.io/badge/GPT--4-412991?style=flat&logo=openai&logoColor=white)

- GPT-4 + text-embedding-3-small 기반 법령·판례 RAG 검색 및 답변 생성
- 유사도 threshold(0.5) 미달 시 OpenLaw API에서 자동 수집·재임베딩 → fallback 재탐색
- AES 메시지 암호화, JWT(액세스 15분 / 리프레시 7~30일), Google·Kakao OAuth2
- Docker + GitHub Actions Blue/Green 무중단 배포  
[🔗 GitHub Repo](https://github.com/ranpia/MoleLaw_backend)

---

### 🩺 MoleComs — 의료 영상 기반 DICOM 관리 서비스
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=flat&logo=springboot&logoColor=white)

- Oracle(DICOM 메타) / MySQL(로그·사용자) 멀티 DB 아키텍처, 트랜잭션 독립 관리
- WebFlux `StreamingResponseBody` 기반 대용량 의료 영상 비동기 스트리밍
- JWT 세션 회전(Session Rotation) + 단일 세션 강제, 의료법 로그 규정 대응  
[🔗 GitHub Repo](https://github.com/24Sahmyook-Team-s1/Molecom_backend)

---

### 🌏 ThinkTrip — 소셜 로그인 + GPT 기반 여행 일정 추천
![Java](https://img.shields.io/badge/Java%2017-ED8B00?style=flat&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot%203.4-6DB33F?style=flat&logo=springboot&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat&logo=nextdotjs&logoColor=white)

- Kakao + Google OAuth2 소셜 로그인, JWT 인증
- GPT 기반 맞춤형 여행 일정 생성, 무료 5회/일 · 프리미엄 무제한 호출 제한
- `startDate` / `endDate` 모델링 + D-Day 자동 계산, Docker + GitHub Actions CI/CD  
[🔗 GitHub Repo](https://github.com/ranpia/thinktrip-back)

---

## 📂 기타 프로젝트
> 그 외 진행한 프로젝트들입니다.

### 📋 MolePMS — JSP 기반 프로젝트 관리 시스템 + CI/CD
- Java 17, JSP, Oracle 11g, Maven
- GitHub Actions + AWS EC2 Docker 자동 배포 (수동 대비 배포 시간 80% 단축)  
[🔗 GitHub Repo](https://github.com/ranpia/molepmsporject)

### 🎮 Fruit Box AI — 게임 자동 플레이 + OCR + 강화학습
- CNN OCR(PyTorch) + ConvDQN 강화학습 + Selenium 브라우저 자동화
- OpenCV HSV 탐지 → 10×17 보드 인식 → 자동 드래그, **최고 170점 달성**  
[🔗 GitHub Repo](https://github.com/ranpia/applegame)

### 📓 MindNote — 감정 분석 일기 앱
- Spring Boot 3.5 / Java 21 + React 19 + GPT-4o 감정 분석
- 감정 트렌드 시각화 + AI 상담 + 콘텐츠 추천  
[🔗 GitHub Repo](https://github.com/ranpia/AI_Diary)

---

## 🏆 Experience & Activities
- 💻 교내 및 외부 팀 프로젝트 5~6개 진행
- ☁️ AWS·GCP 서버 운영 및 배포 경험
- 📊 데이터 분석 및 AI 모델 학습 경험

---

## 📫 Contact
[![Email](https://img.shields.io/badge/Email-richard.kim146%40gmail.com-red?style=flat&logo=gmail&logoColor=white)](mailto:richard.kim146@gmail.com)  
![Profile Views](https://komarev.com/ghpvc/?username=ranpia&color=blueviolet&style=flat)
