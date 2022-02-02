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

        stage('Start Server') {
            steps {
                sh 'mvn spring-boot:start -Dspring-boot.run.arguments=--server.port=8090'
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

                dir('integration_testing') {
                    sh 'npm install'
                    sh 'npm run cy:run'
                }
            }
        }
    }

    stage('Install') {
        steps {
            sh 'mvn install'
        }
    }

    post {
        always {
            sh 'mvn spring-boot:stop'
            dir('integration_testing') {
                sh 'npm run cy:report'
                archiveArtifacts artifacts: "automation-results/", allowEmptyArchive: true
                publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'automation-results', reportFiles: 'martech_automation_report.html', reportName: 'Test Overview', reportTitles: ''])
            }
        }
    }
}
