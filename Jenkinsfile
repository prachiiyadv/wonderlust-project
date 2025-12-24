pipeline {
    agent any

    tools {
        nodejs 'nodejs22'
        jdk 'jdk21'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/prachiiyadv/wonderlust-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('SonarQube Scan') {
    steps {
        withCredentials([string(credentialsId: 'sonar1', variable: 'SONAR_TOKEN')]) {
            withSonarQubeEnv('sonarQube') {
                bat '''
                echo Sonar token injected: %SONAR_TOKEN%
                "C:\\ProgramData\\Jenkins\\.jenkins\\tools\\hudson.plugins.sonar.SonarRunnerInstallation\\SonarScanner\\bin\\sonar-scanner.bat" ^
                -Dsonar.projectKey=wonderlust-project ^
                -Dsonar.projectName=wonderlust-project ^
                -Dsonar.sources=. ^
                -Dsonar.login=%SONAR_TOKEN%
                '''
            }
        }
    }
}

        stage('Trivy Filesystem Scan') {
            steps {
                bat '''
                set TRIVY_DB_REPOSITORY=ghcr.io/aquasecurity/trivy-db
                trivy fs --timeout 15m --severity HIGH,CRITICAL .
                '''
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
