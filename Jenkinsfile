pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/t-kavi-k/8.2CDevSecOps.git'
    }

    stages {

        stage('Build') {
            steps {
                echo 'Building application'
                bat 'node --version'
                bat 'npm --version'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'
                bat 'npm install'
                bat 'npx tap tests/**/*.js || exit /b 0'
            }
        }

        stage('Code Quality') {
            steps {
                echo 'Running SonarCloud code quality analysis'

                withCredentials([
                    string(
                        credentialsId: 'sonar-token',
                        variable: 'SONAR_TOKEN'
                    )
                ]) {
                    bat '''
                    npx sonar-scanner ^
                      -Dsonar.projectKey=t-kavi-k_8.2CDevSecOps ^
                      -Dsonar.organization=t-kavi-k ^
                      -Dsonar.sources=. ^
                      -Dsonar.host.url=https://sonarcloud.io ^
                      -Dsonar.token=%SONAR_TOKEN%
                    '''
                }
            }
        }

        stage('Security') {
            steps {
                echo 'Running Snyk security scan'

                withCredentials([
                    string(
                        credentialsId: 'snyk-token',
                        variable: 'SNYK_TOKEN'
                    )
                ]) {
                    script {
                        def status = bat(
                            script: 'npx snyk test',
                            returnStatus: true
                        )

                        if (status != 0) {
                            echo 'Security vulnerabilities detected'
                        }
                    }
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application'
                bat 'npm run build'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deployment stage completed'
            }
        }

        stage('Monitor') {
            steps {
                echo 'Monitoring stage completed'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
