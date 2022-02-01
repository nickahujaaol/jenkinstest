pipeline {
    agent any
    tools {
        maven 'MAVEN_HOME'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Install') {
            steps {
                sh 'mvn install'
            }
        }

        stage('Start Server') {
            steps {
                sh 'mvn spring-boot:start -Dspring-boot.run.arguments=--server.port=8090'
                echo '################# SERVER STARTED #######################'
            }
        }

        stage('Prepare E2E Testing') {
            steps {
                checkout([$class: 'GitSCM', branches: [
                    [name: '*/main']
                ], extensions: [
                    [$class: 'RelativeTargetDirectory', relativeTargetDir: 'integration_testing']
                ], userRemoteConfigs: [
                    [url: 'https://github.com/nickahujaaol/JenkinsCypress.git']
                ]])

                echo 'Working Dir:'
                sh 'pwd'
            }

            steps {
                dir('integration_testing') {
                    echo 'Working Dir Now:'
                    sh 'pwd'
                    sh 'npm install'
                }
            }
        }

        stage('Run E2E Integration Testing') {
            steps {
                echo '##################### Starting Cypress ###########################'
                sh 'npx cypress run'
                echo '##################### Cypress Complete ###########################'
            }
        }
    }

    post {
        always {
            sh 'mvn spring-boot:stop'
            echo '################# SERVER STOPPED #######################'
        }
    }
}
