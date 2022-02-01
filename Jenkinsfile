pipeline {
    agent any
    tools {
        maven 'MAVEN_HOME'
        nodejs 'NODE_HOME'
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

        stage('Run E2E Testing') {
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

                dir('integration_testing') {
                    echo 'Working Dir Now:'
                    sh 'pwd'
                    sh 'npm install'

                    echo '##################### Starting Cypress ###########################'
                    sh 'npm run cy:run'
                    echo '##################### Cypress Complete ###########################'
                    sh 'ls'
                }
            }
        }
    }

    post {
        always {
            sh 'mvn spring-boot:stop'
            echo '################# SERVER STOPPED #######################'
            sh 'npm run cy:report'
            archiveArtifacts artifacts: "collections/martech-tests/automation-results/", allowEmptyArchive: true
            publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'collections/martech-tests/automation-results', reportFiles: 'martech_automation_report.html', reportName: 'Test Overview', reportTitles: ''])
        }
    }
}
