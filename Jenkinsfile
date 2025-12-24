pipeline {
    agent any

    environment {
        IMAGE_NAME = "wanderlust-backend"
        IMAGE_TAG  = "1.0"
        SONAR_SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Workspace Cleanup') {
            steps {
                cleanWs()
            }
        }

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/prachiiyadv/wonderlust-project.git'
                    // credentialsId: 'github-creds'   // enable if repo is private
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ./backend
                '''
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh '''
                trivy image --exit-code 1 --severity HIGH,CRITICAL ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('SonarQube Code Scan') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh """
                    ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=wanderlust-backend \
                    -Dsonar.sources=backend \
                    -Dsonar.host.url=$SONAR_HOST_URL \
                    -Dsonar.login=$SONAR_AUTH_TOKEN
                    """
                }
            }
        }

        stage('SonarQube Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            slackSend(
                channel: '#ci-cd',
                color: 'good',
                message: "✅ Jenkins Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }

        failure {
            slackSend(
                channel: '#ci-cd',
                color: 'danger',
                message: "❌ Jenkins Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }

        always {
            cleanWs()
        }
    }
}
stage('Webhook Test') {
    steps {
        echo 'CI/CD webhook triggered successfully'
    }
}
