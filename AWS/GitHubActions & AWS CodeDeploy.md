# GitHub-Actions 과 AWS CodeDeploy 를 이용한 CI/CD 구축
: GitHub-Actions과 AWS CodeDeploy를 이용하여 CI/CD 환경을 구축하기 위해서는 다음과 같은 세팅이 필요합니다. 
1. 어플리케이션이 실행될 EC2에 AWS CodeDeploy Agent 설치
2. AWS CodeDeploy 를 위한 역할 생성
3. EC2에 S3에 접근할 수 있는 역할 생성하여 세팅
4. AWS CodeDeploy 생성 & 배포그룹 생성
5. GitHub-Actions 으로 빌드한 파일을 업로드할 AWS S3 생성
6. GitHub-Actions 을 위한 역할 생성
7. GitHub-Actions 생성
8. AWS - 자격 증명 공급자 추가 


## 1. EC2에 AWS CodeDeploy Agent 설치
1. EC2 터미널에 접속한 후, `sudo yum update`로 먼저 패키지를 업데이트 하고 `sudo yum install ruby` 명령어로 루비를 설치합니다.

![](../img/aws/ec2-codeDeploy-01.png)

2. `sudo yum install wget` 명령어를 실행하고, `cd /home/ec2-user`로 홈 디렉토리로 경로를 이동한 후, <br> `wget https://본인리전의 버킷명.s3.리전-식별자.amazonaws.com/latest/install`
   리전의 버킷명과 식별자는 해당 링크에서 확인할 수 있습니다. https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/resource-kit.html#resource-kit-bucket-names
3. `chmod +x ./install` -> install 파일에 대한 실행 권한을 설정합니다.
4. `sudo ./install auto` -> 최신 버전의 CodeDeploy 에이전트 설치

![](../img/aws/ec2-codeDeploy-02.png)

5. `sudo service codedeploy-agent status` -> CodeDeploy 에이전트가 정상적으로 설치되어 실행 중인지 확인, 'The AWS CodeDeploy agent is running' 메시지가 뜨면 정상적으로 설치된 것입니다.

![](../img/aws/ec2-codeDeploy-03.png)


## 2. AWS CodeDeploy 를 위한 역할 생성
1. AWS 콘솔화면에서 `IAM` -> `역할` -> `역할만들기`를 클릭합니다.

![](../img/aws/aws-role-01.png)

2. 신뢰할 수 있는 엔티티에서 `AWS 서비스`를 선택하고, `CodeDeploy`를 검색하여 선택한 후 `다음`을 클릭합니다.

![](../img/aws/aws-role-02.png)

3. 세팅 된 권한을 확인하고 `다음`을 클릭합니다.

![](../img/aws/aws-role-03.png)

4. 의미있는 이름으로 역할 이름을 입력하고, 역할을 생성합니다.

![](../img/aws/aws-role-04.png)
![](../img/aws/aws-role-05.png)

## 3. EC2에서 S3에 접근할 수 있는 역할 생성 & 할당

1. 위에서 했던 것과 동일하게 AWS 콘솔화면에서 `IAM` -> `역할` -> `역할만들기`를 클릭합니다.

![](../img/aws/aws-role-01.png)

2. 이번에는 `AWS 서비스`를 선택한 후 `EC2`를 선택하여 `다음`을 클릭합니다.

![](../img/aws/aws-role-06.png)

3. 그리고 EC2에서 S3에 접근할 수 있도록 `AmazonS3FullAccess` 권한을 검색하여 선택한 뒤 `다음`을 클릭합니다.

![](../img/aws/aws-role-07.png)

4. 역할에 대한 의미있는 이름을 입력하고 역할을 생성합니다.

![](../img/aws/aws-role-08.png)

5. EC2 대시보드 화면으로 이동하여 역할을 부여할 EC2 인스턴스를 선택하고, `작업` -> `보안` -> `IAM 역할 수정`을 클릭합니다.

![](../img/aws/aws-role-09.png)

6. IAM 역할에서 위에서 추가한 역할을 선택하고 `IAM 역할 업데이트`를 클릭합니다.

![](../img/aws/aws-role-10.png)

7. 연결 완료 메세지가 뜨면 역할이 정상적으로 연결된 것으로 이제 EC2에서 S3에 정상적으로 접근할 수 있습니다.

![](../img/aws/aws-role-11.png)


## 4. AWS CodeDeploy 생성 & 배포그룹 생성 
1. AWS 서비스중 CodeDeploy를 검색하여 애플리케이션 생성을 클릭합니다.

![](../img/aws/aws-codedeploy-01.png)

2. 의미있는 deploy 애플리케이션 이름을 입력하고 컴퓨팅 플랫폼은 EC2/온프레미스를 선택하여 `애플리케이션 생성`을 클릭합니다.

![](../img/aws/aws-codedeploy-02.png)

3. 그러면 애플리케이션이 생성되었다는 메세지가 뜨는 것을 확인할 수 있습니다.

![](../img/aws/aws-codedeploy-03.png)

4. 다음으로는 배포를 위한 배포그룹을 생성해야 합니다. 위에서 생성한 deploy 애플리케이션으로 들어가서 `배포 그룹` -> `배포 그룹 생성`을 클릭합니다.

![](../img/aws/aws-codedeploy-04.png)

5. 배포 그룹 이름을 입력하고, 위에서 생성한 AWS CodeDeploy를 위한 역할을 서비스 역할에 입력합니다. 그리고 배포 방법은 `현재 위치`로 선택합니다. 

![](../img/aws/aws-codedeploy-05.png)

6. 환경 구성은 `Amazon EC2 인스턴스`를 클릭하고 원하는 EC2의 태그 그룹 키값을 입력합니다. 
   - EC2의 태그는 EC2 > 인스턴스 > 애플리케이션 > 태그 > 태그 관리에서 추가할 수 있습니다.

![](../img/aws/aws-codedeploy-06.png)

7. AWS CodeDeploy 에이전트의 최신버전 유지를 위해 14일 주기로 업데이트 되도록 설정했고, 배포 구성은 `AllAtOnce`로 설정했습니다.<br>
   저의 경우 로드 밸런싱을 하지 않는 환경이므로 `로드 밸런싱 활성화`는 체크 해제 후 `배포 그룹 생성`을 클릭하면 됩니다.
   - 배포 구성에 대한 설명은 해당 링크를 참조하시면 됩니다. https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/deployment-configurations.html

![](../img/aws/aws-codedeploy-07.png)

8. 배포 그룹 생성 메세지가 뜨면 성공 적으로 생성 된 것입니다.

![](../img/aws/aws-codedeploy-08.png)

## 5. AWS S3 생성
1. AWS CodeDeploy 는 말그대로 배포를 담당하는 애플리케이션이기때문에 빌드를 통해 생성된 jar 파일을 저장할 저장소를 갖고 있지 않습니다. 따라서 jar 파일을 저장해 놓을 스토리지인 AWS S3가 필요합니다. <br>
   S3를 생성하기 위해서는 AWS 콘솔에 로그인 > S3 검색 > 버킷 만들기를 클릭합니다.

![](../img/aws/aws-s3-01.png)

2. 버킷의 이름을 입력하고 리전은 EC2가 위치한 리전과 동일한 리전으로 선택해줍니다. 객체의 소유권은 기본 세팅된 옵션인 `ACL 비활성화됨` 으로 선택합니다.

![](../img/aws/aws-s3-02.png)

3. 해당 스토리지는 빌드 파일을 업로드하는 용도이기때문에 외부에서 접근할 수 없도록 `모든 퍼블릭 액세스 차단`을 선택하고, 버전 관리도 `비활성화`로 선택합니다.

![](../img/aws/aws-s3-03.png)

4. 암호화 세팅은 `SSE-S3를 사용한 서버측 암호화`를 선택하고 버킷키는 `비활성화`를 선택했습니다. 마지막으로 `버킷 만들기`를 클릭하면 S3가 생성됩니다.
![](../img/aws/aws-s3-04.png)

## 6. GitHub-Actions 을 위한 역할 생성


## GitHub-Actions 설정하기 
1. 


![](../img/aws/github-actions-01.png)

2. 

![](../img/aws/github-actions-02.png)

3. 

```yaml
# This workflow uses actions that are not certified by GitHub.
# They are provided by a third-party and are governed by
# separate terms of service, privacy policy, and support
# documentation.
# This workflow will build a Java project with Gradle and cache/restore any dependencies to improve the workflow execution time
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-java-with-gradle

name: Java CI with Gradle

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

env:
  AWS_REGION: ap-northeast-2
  S3_BUCKET_NAME: house-rent-s3
  CODE_DEPLOY_APPLICATION_NAME: house-rent-deploy
  CODE_DEPLOY_DEPLOYMENT_GROUP_NAME: house-rent-deploy-agent
  
permissions:
  id-token: write
  contents: read

jobs:
  build:

    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Set up JDK 17
      uses: actions/setup-java@v3
      with:
        java-version: '17'
        distribution: 'temurin'
        
    - name: Run chmod to make gradlew executable
      run: chmod +x ./gradlew
      
    - name: Gradel Clean
      run: ./gradlew clean
      
    - name: Build with Gradle
      run: ./gradlew build
    
    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v3
      with:
        aws-region: ${{env.AWS_REGION}}
        role-to-assume: arn:aws:iam::634903875227:role/sts.amazonaws.com

  # s3 업로드
    # AWS S3에 업로드
    - name: Upload to AWS S3
      run: |
        aws deploy push \
          --application-name ${{ env.CODE_DEPLOY_APPLICATION_NAME }} \
          --ignore-hidden-files \
          --s3-location s3://$S3_BUCKET_NAME/$GITHUB_SHA.zip \
          --source .

    # AWS EC2에 Deploy
    - name: Deploy to AWS EC2 from S3
      run: |
        aws deploy create-deployment \
          --application-name ${{ env.CODE_DEPLOY_APPLICATION_NAME }} \
          --deployment-config-name CodeDeployDefault.AllAtOnce \
          --deployment-group-name ${{ env.CODE_DEPLOY_DEPLOYMENT_GROUP_NAME }} \
          --s3-location bucket=$S3_BUCKET_NAME,key=$GITHUB_SHA.zip,bundleType=zip

```


### References
[Spring(Gradle)/MySQL + github action + AWS(S3, EC2, CodeDeploy) 사용하여 CI/CD 구축하기](https://velog.io/@donghokim1998/SpringMySQL-github-action-AWSS3-EC2-CodeDeploy-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-CICD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0#12-codedeploy-%EC%83%9D%EC%84%B1) <br>
[Github Action과 AWS CodeDeploy를 사용한 CI/CD 구축 방법](https://chae528.tistory.com/100) <br>
[CodeDeploy 에이전트 설치](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/codedeploy-agent-operations-install.html)
