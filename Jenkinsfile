pipeline {
  agent any
  tools { 
    maven 'MAVEN_HOME' 
  }
  
  stages {
    stage ('Build') {
      steps {
        sh 'mvn clean compile'
      }
    }

    stage ('Test') {
      steps {
        sh 'mvn test'
      }
    }

    stage ('Install') {
      steps {
        sh 'mvn install'
      }
    }
    
    stage ('Start Server') {
      steps {
        sh 'mvn spring-boot:start'
        echo '################# SERVER STARTED #######################'
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
