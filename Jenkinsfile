pipeline {
  agent any
  tools {
    maven 'Maven'
  }
  
  stages {
    stage('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    stage('Check-Git-Secret') {
      steps {
        sh 'rm git_secret_output || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/sahil3112/webapp.git > git_secret_output'
        sh 'cat git_secret_output'
      }
    }
    stage('Build') {
      steps {
      sh 'mvn clean package'
      }
    }
    stage ('Deploy-To-Tomcat') {
            steps {
             sh 'whoami'
                sh 'cp target/*.war /home/jenkins/apache-tomcat-8.5.69/webapps/webapp.war' 
        }
    }
  }
}
