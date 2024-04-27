pipeline {
    agent any
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
        stage('init SonarQube') {
            steps {
                sh 'mvn sonar:sonar -Dsonar.login=admin -Dsonar.password=admin' // admin admin or admin sonar
            }
        }
        stage("SonarQube Quality Gate Check") {
            steps {
                script {
                def qualityGate = waitForQualityGate()
                    
                    if (qualityGate.status != 'OK') {
                        echo "${qualityGate.status}"
                        error "Quality Gate failed: ${qualityGateStatus}"
                    }
                    else {
                        echo "${qualityGate.status}"
                        echo "SonarQube Quality Gates Passed"
                    }
                }
            }
        }
        stage('Nexus Deployment') {
            steps {
                sh 'mvn deploy -DskipTests'
            }
        }
        stage('Building Image') {
            steps {
                sh 'docker build -t JihedMrouki/Ski .'
            }
        }
        stage('Pushing Image') {
            steps {
                sh 'echo "logging to docker" | docker login -u JihedMrouki --password-stdin'
                sh 'docker tag JihedMrouki/ski JihedMrouki/ski:latest'
                sh 'docker push JihedMrouki/ski:latest'
            }
        }
    }
    post {
        always {
            emailext (
                to: 'jihed.mrouki@gmail.com',
                subject: "Pipeline ${currentBuild.fullDisplayName} completed",
                body: "The pipeline ended with result: ${currentBuild.result}",
                mimeType: 'text/plain'
            )
        }
    }
}