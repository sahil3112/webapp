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
     stage ('SCA (OWASP-Dependency-Check)') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /home/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
    stage ('SAST (SonarQube)') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }
    stage ('Check-Git-Secrets (Git Secret trufflehog)') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehog'
        sh 'cat trufflehog'
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
    stage ('DAST (OWASP ZAP)') {
      steps {
        sshagent(['zap']) {
         sh '"docker run -t owasp/zap2docker-stable zap-baseline.py -t http://10.0.2.15:8090/webapp/" || true'
        }
      }
    }
  }
}
