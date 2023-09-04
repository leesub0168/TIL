# GitHub-Actions 과 AWS CodeDeploy 를 이용한 CI/CD 구축
: GitHub-Actions과 AWS CodeDeploy를 이용하여 CI/CD 환경을 구축하기 위해서는 다음과 같은 세팅이 필요합니다. 
1. 어플리케이션이 실행될 EC2에 AWS CodeDeploy Agent 설치
2. AWS CodeDeploy 생성
3. GitHub-Actions 으로 빌드한 파일을 업로드할 AWS S3 생성
4. GitHub-Actions 을 위한 role 생성
5. AWS CodeDeploy 를 위한 role 생성 


## AWS CodeDeploy 설정하기


## GitHub-Actions 설정하기 
1. 


![](../img/aws/github-actions-01.png)

2. 

![](../img/aws/github-actions-02.png)

3. dsfgsdg

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
[Github Action과 AWS CodeDeploy를 사용한 CI/CD 구축 방법](https://chae528.tistory.com/100)
