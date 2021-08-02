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
    stage('SCA') {
      steps {
        sh 'rm owasp* || true'
        sh 'wget "https://raw.githubusercontent.com/sahil3112/webapp/master/owasp-dependency-check.sh"'
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'bash owasp-dependency-check.sh'
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
