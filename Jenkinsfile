pipeline {
    agent any

    environment {
        DEV_SERVER = 'DEV_SERVER'
        REMOTE_DIR = '/home/ubuntu/karan'
        BITBUCKET_CREDENTIALS = credentials('bitbucket-credentials')
        SONAR_LOGIN = credentials('sonar-login')
        SONARQUBE_URL = 'http://65.1.49.241:9000'
        SCANNER_HOME = tool name: 'SonarScanner'
        SONAR_PASSWORD = credentials('sonar-password')
    }

    stages {
        stage('Clean Workspace (Start)') { // Initial cleanup
            steps {
                echo 'Cleaning workspace before starting...'
                cleanWs deleteDirs: true
            }
        }

        stage('Git Pull') {
            steps {
                echo 'Pulling latest code from the Bitbucket repository...'
                git branch: 'main', credentialsId: 'bitbucket-credentials', url: 'https://bitbucket.org/karanravat60/f.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withSonarQubeEnv('SonarQube') {
                        sh """
                            cd \${WORKSPACE} && ${scannerHome}/bin/sonar-scanner \\
                            -Dsonar.projectKey=karanravat60_f_AZA5Z3IOGN-ceHoXxFx1 \\
                            -Dsonar.host.url=${SONARQUBE_URL} \\
                            -Dsonar.login=${env.SONAR_LOGIN} \\
                            -Dsonar.password=${env.SONAR_PASSWORD}
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Clean Workspace (End)') { // Final cleanup
            steps {
                echo 'Cleaning workspace after completing the build...'
                cleanWs deleteDirs: true
            }
        }
    }
}
