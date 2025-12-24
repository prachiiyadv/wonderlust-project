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
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('sonarQube') {
                        bat """
                        "${scannerHome}\\bin\\sonar-scanner.bat" ^
                        -Dsonar.projectKey=wonderlust-project ^
                        -Dsonar.projectName=wonderlust-project ^
                        -Dsonar.sources=.
                        """
                    }
                }
            }
        }

        stage('Trivy Filesystem Scan') {
    steps {
        bat '''
        set TRIVY_CACHE_DIR=%WORKSPACE%\\.trivycache
        trivy fs ^
          --timeout 15m ^
          --cache-dir %TRIVY_CACHE_DIR% ^
          --severity HIGH,CRITICAL ^
          .
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
