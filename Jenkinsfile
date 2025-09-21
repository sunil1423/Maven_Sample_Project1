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
                        -Dsonar.projectKey=myproject-%BRANCH_NAME% ^
                        -Dsonar.projectName=Project-%BRANCH_NAME% ^
                        -Dsonar.sources=src ^
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    script {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Quality Gate failed!"
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "SUCCESS: Pipeline completed successfully!"
        }
        failure {
            echo "FAILED: Pipeline failed!"
        }
    }
}
