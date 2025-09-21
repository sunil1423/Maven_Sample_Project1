pipeline {
    agent any

    // tools {
    //     maven 'maven-3.9.9'   // Jenkins Maven tool (configure in Global Tools)
    //     jdk 'JDK17'         // Jenkins JDK tool
    // }

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

        // stage('SonarQube Analysis') {
        //     steps {
        //         withSonarQubeEnv('MySonarQube') {
        //             bat """
        //                 ${SCANNER_HOME}/bin/sonar-scanner \
        //                 -Dsonar.projectKey=myproject-${env.BRANCH_NAME} \
        //                 -Dsonar.projectName=MyProject-${env.BRANCH_NAME} \
        //                 -Dsonar.sources=src \
        //                 -Dsonar.java.binaries=target/classes
        //             """
        //         }
        //     }
        // }
        stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('MySonarQube') {
            bat """
                %SCANNER_HOME%\\bin\\sonar-scanner ^
                -Dsonar.projectKey=myproject-%BRANCH_NAME% ^
                -Dsonar.projectName=MyProject-%BRANCH_NAME% ^
                -Dsonar.sources=src ^
                -Dsonar.java.binaries=target/classes
            """
        }
    }
}


        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                     waitForQualityGate abortPipeline: true
                }
            }
        }    
    }
}
       
