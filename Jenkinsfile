pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS = credentials('docker-hub')
  }
  stages {
    stage('Maven Compile') {
      steps {
        sh 'mvn compile'
      }
    }
    stage('Test JUnit') {
      steps {
        sh 'mvn test -Dtest=PisteServicesImplTest'
      }
    }
    stage('Maven Build') {
      steps {
        sh 'mvn clean install -DskipTests'
      }
    }
    stage('SonarQube') {
      steps {
        sh 'mvn sonar:sonar -Dsonar.login=admin -Dsonar.password=sonar'
      }
    }
    stage('Nexus Deployment') {
      steps {
        sh 'mvn deploy -DskipTests'
      }
    }
    stage('Building Image') {
      steps {
        sh 'docker build -t jihedmrouki/ski .'
        //sh 'docker build -t $DOCKERHUB_CREDENTIALS_USR/ski:$BUILD_ID'
      }
    }
    stage('Pushing Image') {
      steps {
        //sh 'echo "logging to docker" | docker login -u JihedMrouki --password-stdin' // to be removed or replaced by token login
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        //sh 'docker tag $DOCKERHUB_CREDENTIALS_USR/ski jihedmrouki/ski:latest'
        //sh 'docker push $DOCKERHUB_CREDENTIALS_USR/ski:latest'
        sh 'docker push $DOCKERHUB_CREDENTIALS_USR/ski:$BUILD_ID'
        //sh 'docker tag JihedMrouki/ski JihedMrouki/ski:latest'
        //sh 'docker push JihedMrouki/ski:latest'

      }
    }
    stage('Cleanup') {
      steps {
        sh 'docker rmi $DOCKERHUB_CREDENTIALS_USR/ski:$BUILD_ID'
        sh 'docker logout'
      }
    }
  }
}