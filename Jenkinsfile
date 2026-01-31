pipeline {
    agent any

    tools {
        jdk 'jdk-17'          // Configure in Jenkins → Global Tool Configuration
        maven 'maven-3.9.12'     // Configure in Jenkins → Global Tool Configuration
    }

    environment {
        SONARQUBE_SERVER = 'SonarQube'   // Jenkins → Configure System → SonarQube
    }

    stages {

        stage('Checkout Source') {
            steps {
                git branch: 'master',
                    credentialsId: 'jenkins-ssh',
                    url: 'git@github.com:brahmaji99/maven-simple.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh '''
                      mvn sonar:sonar \
                      -Dsonar.projectKey=my-java-app \
                      -Dsonar.projectName=my-java-app \
                      -Dsonar.java.binaries=target
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo "✅ Code analysis passed Quality Gate"
        }
        failure {
            echo "❌ Code analysis failed"
        }
    }
}
