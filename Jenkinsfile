pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'SonarScanner'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean install -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarQube') {
                    bat """
                        %SCANNER_HOME%\\bin\\sonar-scanner ^
                        -Dsonar.projectKey=my-java-project-%BRANCH_NAME% ^
                        -Dsonar.projectName=MyJavaProject-%BRANCH_NAME% ^
                        -Dsonar.sources=src/main/java ^
                        -Dsonar.java.binaries=target/classes ^
                        -Dsonar.login=%SONAR_AUTH_TOKEN%
                    """
                }
            }
        }

        stage('Quality Check') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {  // Timeout added
                    script {
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ SUCCESS: Pipeline completed successfully!"
        }
        failure {
            echo "❌ FAILED: Pipeline failed!"
        }
    }
}
