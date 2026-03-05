# 이철의 포트폴리오
***
## 📞 Contact
- 이메일 : saparationlee@gmail.com
- 블로그 : https://saparation.tistory.com/

---
## 📑 Projects
### 1. XR Interactive Content Framework

실감형 체험존의 벽과 바닥 전체에 프로젝션 매핑을 적용하고, 사용자 인터랙션이 가능한 XR 콘텐츠 개발

<details>
<summary>기술 스택 및 개발 환경</summary>
  
| 구분 | 내용 |
|-----|-----|
| 개발 툴 & 언어 | Unreal Engine 5.3 (C++ & Blueprint) |
| 패키징 플랫폼 | Windows |
| 플러그인 | Spout, OSC |
| 외부 소프트웨어 | HOKUYO Manager, Resolume Arena |
| 외부 하드웨어 | LiDAR Sensor, 빔 프로젝터 |
| 버전 관리 | GitHub |

</details>

### Architecture & Design

시스템 설계의 목적은 아래와 같습니다. 

1. 개발팀원들이 프로젝트의 주요 시스템에 대한 최소한의 이해만으로도 콘텐츠 개발이 가능하도록 설계
2. 모든 체험형 레벨에서 동일한 개발 방식을 적용하여 팀원 간 유지보수와 테스트, 협업이 용이하도록 설계
3. 여러 프로젝트에서 공통적으로 사용되는 OSC 및 Spout 기능을 Unreal Plugin 형태로 개발하여 모듈화

<details>
  <summary><strong> 🏗️ System Architecture</strong></summary>
  <br>
<img width="1150" height="912" alt="image" src="https://github.com/user-attachments/assets/9ae7a7ad-7aef-4bc5-8041-31ce6765c7ff" />  
</details>

<details>
  <summary><strong> 📝 Framework</strong></summary>
  <br>
  
1. 개발 효율성
   - **온보딩 단순화**
     - 설계 의도 : 개발 팀원은 DataTable, BoardManager, BoardRule, PlayerActor의 작동 방식만 이해하면 즉시 서브 레벨 개발에 투입 가능
     - 효과 : 시스템 복잡도를 낮추어 개발 팀원의 온보딩 시간 단축

   - **공통 기능 모듈화**
     - 설계 의도 : 여러 프로젝트에서 공통적으로 사용되는 기능을 플러그인 형태로 분리하여 모듈화
     - 효과 : 다른 프로젝트에서도 별도의 구현 없이 OSC 및 Spout 기능을 즉시 사용할 수 있도록 재사용성 확보

2. 확장성
   - 설계의도 : DataTable 을 활용한 콘텐츠 확장에 용이한 구조 채택
   - 효과 : 코드 수정 없이 데이터 테이블 편집으로 새로운 오브젝트 추가 가능

3. 안정성
   - 설계의도 : CreateObjPoints 내 오브젝트 풀링 구현
   - 효과 : 실시간 센서 인터렉션 시 빈번한 Actor 생성/파괴로 인한 오버헤드 방지

4. 객체지향 설계
   - 설계의도 : BoardRule 추상 클래스로 설계 및 BoardManager 분리
   - 효과 : 다형성 - LevelManager 는 공통 메서드만 호출하여 각 룰을 독립적으로 실행
   - 캡슐화 - BoardManager과 BoardRule을 역할을 분리하여 서브 레벨과 Root 레벨 간의 의존성 최소화
  
<br>

    
<img width="2021" height="1331" alt="ClassDiagramSportrack3차최종 drawio" src="https://github.com/user-attachments/assets/e70b6136-3d2d-4a98-9cd3-4ae7c0233b08" />


</details>

<details>
<summary><strong> 🚧 Sequence Diagram & Troubleshooting </strong></summary>

<br>

**1. 레벨 스위칭 시퀀스**
   
     
<img width="661" height="481" alt="레벨스위칭시퀀스 drawio (2)" src="https://github.com/user-attachments/assets/7a2de6df-4890-4564-893d-d7387e4fa642" />

## Issue #1

GPU Crash during Level Streaming
- 원인 : 레벨 전환 시 이전 레벨의 텍스처가 전부 해제 되지 않고 새로운 레벨의 텍스처가 로드되면서 VRAM 사용량 급격히 증가.
     
- 문제 분석 : memreport 분석 결과, 일부 서브 레벨의 텍스처가 NPOT(Non Power Of Two) 형태로 제작되어 MipMap이 생성되지 않아 텍스처 스트리밍이 정상적으로 동작하지 않고 VRAM 사용량이 비정상적으로 증가하는 문제 확인
     
- 해결 과정 : 그래픽 디자이너 팀과 협업하여 해당 텍스처들을 POT(Power Of Two) 형태로 재제작하고 압축 설정 및 텍스처 그룹을 UIInterface → World 로 변경하여 스트리밍 가능하도록 수정
     
- 결과 : 레벨 전환 시 VRAM 사용량을 약 15GB → 8GB 수준으로 감소
  
</details>
