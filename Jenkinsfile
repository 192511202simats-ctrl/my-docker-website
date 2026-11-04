
pipeline {
    agent any

    triggers {
        pollSCM('H/1 * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"C:/Users/HP/AppData/Local/Programs/DockerDesktop/resources/bin/docker.exe" build -f dockerfile -t my-static-website:%BUILD_NUMBER% .'
            }
        }

        stage('Test Website') {
            steps {
                bat '"C:/Users/HP/AppData/Local/Programs/DockerDesktop/resources/bin/docker.exe" run -d --name website-test -p 8082:80 my-static-website:%BUILD_NUMBER%'
                bat 'powershell -NoProfile -Command "Invoke-WebRequest http://localhost:8082 -UseBasicParsing | Out-Null"'
            }
        }

        stage('Deploy Website') {
            steps {
                bat '"C:/Users/HP/AppData/Local/Programs/DockerDesktop/resources/bin/docker.exe" rm -f my-website 2>NUL || exit /b 0'
                bat '"C:/Users/HP/AppData/Local/Programs/DockerDesktop/resources/bin/docker.exe" run -d --restart unless-stopped -p 8081:80 --name my-website my-static-website:%BUILD_NUMBER%'
            }
        }
    }

    post {
        always {
            bat '"C:/Users/HP/AppData/Local/Programs/DockerDesktop/resources/bin/docker.exe" rm -f website-test 2>NUL || exit /b 0'
        }

        success {
            echo 'Website deployed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}

