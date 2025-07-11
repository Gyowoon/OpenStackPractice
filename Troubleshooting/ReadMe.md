## OpenStack 실습 중 발생한 오류/문제를 해결하는 과정을 기록하고 공유합니다. 
### 실습 환경 및 PC 설정 
- 프로세서	12th Gen Intel(R) Core(TM) i5-12400   2.50 GHz
- RAM	64.0GB(63.7GB 사용 가능)
- 시스템 종류	64비트 운영 체제, x64 기반 프로세서
- VirtualBox version 7.1.10 r169112 (Qt6.5.3)

## Table of Contents
 1. [Horizon 접속 불가](#Horizon-접속-불가)
 2. [Subheading 2](#subheading-2)
 3. [Subheading 3](#sub-heading-3)
 4. [Subheading 4](#sub-heading-4)
 5. [Subheading 5](#sub-heading-5)
 6. [Subheading 6](#sub-heading-6)

 ## Horizon 접속 불가
 - 문제 상황 : Host PC의 브라우저로 http://localhost:10000 입력 시 아래와 같은 화면 출력됨
   <img width="1319" height="232" alt="image" src="https://github.com/user-attachments/assets/ee7fe5b1-58c2-4839-a60c-642b8f5a9061" />
- [관련 영상](https://youtu.be/tzLplSuLnq4?si=kN_VQNzjqWSO00HN&t=1577)
-  해결 시도 :
    - Apache error log 확인(/var/log/apache2/openstack_dashboard-error.log)
        - `ModuleNotFoundError: No module named 'django_pyscss'` 발견
        - ➡️ Django 모듈 일부가 설치되지 않아 wsgi 로딩 실패로 간주
    - Horizon 정상 동작을 위해 필요한 패키지 `django-pyscss` 가 `pyScss<1.3.0` 에 의존함을 확인
        - `pyScss<1.3.0` 버전 설치 시도 ; `ImportError: cannot import name 'Feature' from 'setuptools'` 발견 
        - ➡️ pyScss 1.2.1 내부에서 setuptools.Feature 라는 Class 사용, `setuptools` 5.7 ver 부터는 해당 Class 삭제로 인한 문제로 판단
        - 
     
----------------------------------
2-2. Django 호환 버젼으로 Downgrade `pip3 install "django>=3.2,<3.3"`
-> 공식 배포판 setup.py 내부에 `Feature` 관련 코드 존재로 ImportError 지속
--> setup.py 파일 수정,  `Feature` 관련 코드 삭제
-> 수정된 setup.py 파일로 pyScss 수동 설치 `python3 setup.py install` 
- 결론 및 분석: Horizon이 사용하는 Django 기반의 정적 리소스 처리(특히 SCSS → CSS 변환)에 필요한 패키지들이 Python/Django 최신 버전과 호환되지 않아 생긴 문제 ➡️ 버전 맞춤 및 수동 조치로 해결

4. 관련 Dependency 설치 및 setuptools  
### 해결 방안 : setup.py 파일 신규 생성 

 ## Subheading 2
 Content of the subheading 2
 ## Sub heading 3
 Content of the subheading 3
 ## Subheading 4
 Content of the subheading 4
 ## Sub heading 5
 Content of the subheading 5
 ## Sub heading 6
 Content of the subheading 6
