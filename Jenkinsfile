pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/TuanAnhGoPro/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'npm test || exit /b 0'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    bat '''
                        if exist sonar-scanner rmdir /s /q sonar-scanner
                        if exist sonar-scanner-cli.zip del sonar-scanner-cli.zip

                        curl -sSLo sonar-scanner-cli.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-7.0.2.4839-windows-x64.zip
                        powershell -Command "Expand-Archive -Path sonar-scanner-cli.zip -DestinationPath ."
                        ren sonar-scanner-7.0.2.4839-windows-x64 sonar-scanner

                        set PATH=%PATH%;%CD%\\sonar-scanner\\bin

                        sonar-scanner.bat ^
                          -Dsonar.projectKey=TuanAnhGoPro_8.2CDevSecOps ^
                          -Dsonar.organization=tuananhgopro ^
                          -Dsonar.sources=. ^
                          -Dsonar.host.url=https://sonarcloud.io ^
                          -Dsonar.login=%SONAR_TOKEN%
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully. Check SonarCloud dashboard.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}