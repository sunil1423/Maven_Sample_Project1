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
                        -Dsonar.projectName=MyJava-Project-%BRANCH_NAME% ^
                        -Dsonar.sources=src/main/java ^
                        -Dsonar.java.binaries=target/classes ^
                        -Dsonar.login=%SONAR_AUTH_TOKEN%
                    """
                }
            }
        }

        stage('Quality Check') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
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
