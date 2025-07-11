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
 - 문제 상황 : Host PC의 브라우저로 http://localhost:10000 접속 시 아래와 같은 화면 출력됨
   <img width="1319" height="232" alt="image" src="https://github.com/user-attachments/assets/ee7fe5b1-58c2-4839-a60c-642b8f5a9061" />
- [관련 영상](https://youtu.be/tzLplSuLnq4?si=kN_VQNzjqWSO00HN&t=1577)
-  해결 과정 :
    - Apache error log 확인(/var/log/apache2/openstack_dashboard-error.log)
        - `ModuleNotFoundError: No module named 'django_pyscss'` 발견 <br>
          ➡️ Django 모듈 일부가 설치되지 않아 wsgi 로딩 실패로 간주
    - Horizon (23.1/Antelope) 정상 동작을 위한 필요 패키지 설치
        - Official Repository에서 `requirements.txt`를 다운받아 아래 코드 입력 
        - ```bash
          cd /var/www/horizon
          curl -O https://opendev.org/openstack/horizon/raw/branch/unmaintained/2023.1/requirements.txt
          pip3 install -r requirements.txt

          
          python3 manage.py collectstatic --noinput
          python3 manage.py compress --force 
          systemctl restart apache2

          ```
      - 문제 해결 😄 (정상 접속 및 로그인 가능 확인) 
    
<details>

<summary>삽질 과정</summary>

1. Apache error log 확인 후 django_pyscss 패키지 설치 시도
+ GPT 질문 시, `pip3 install "django-pyscss<2.0" "pyScss<1.3.0"` 버젼 설치 권고받음 
2. `pip3 install "pyScss<1.3.0"` 입력 후 `ImportError: cannot import name 'Feature' from 'setuptools' ` 오류 발생
+ 설치 시 참조되는 setup.py 내부에서 setuptools.Feature 라는 Class 사용하는데, `setuptools` 5.7 ver 부터는 해당 Class 삭제된 확인
3. setup.py 內 Feature 관련 코드 완전 삭제 후 아래 코드 입력 , 이후 다음 오류 발생 
  ```bash
  python3 setup.py install`
  cd /var/www/horizon
  sudo python3 manage.py collectstatic --noinput
  sudo python3 manage.py compress

  # 출력 생략
  ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour
  is the source of the following dependency conflicts.
  horizon 0.0.0 requires django-pyscss>=2.0.2, but you have django-pyscss 1.0.6 which is incompatible.
  horizon 0.0.0 requires pyScss>=1.4.0, but you have pyscss 1.2.1 which is incompatible.
  ```
+ 현재 설치된 `pyScss` 및 `django-pyscss`가 Horizon의 요구사항에 맞지 않음(호환X) 확인
+ 근본적으로 해당 방법은 해결책이 아니였음 <br>
  ➡️ Horizon (23.1/Antelope) 정상 동작을 위한 필요 패키지 설치 진행 

</details>

- 결론 및 분석: Horizon이 요구하는 Pakage가 제대로 갖춰지지 않아 발생한 문제 <br>
  ➡️ 공식 repo의 requirements.txt 기반 패키지 설치로 해결
     
----------------------------------


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
