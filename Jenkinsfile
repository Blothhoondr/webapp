pipeline {
  agent any 
  tools {
    maven 'Maven'
  }

  stages {
    stage ('Intialise') {
      steps {
        sh '''
                echo "PATH = ${PATH}"
                echo "M2_HOME = ${M2_HOME}"
        '''
      }
    }
    
    stage ('Check-Git-Secrets') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/Blothhoondr/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    
    stage ('Source-Composition-Analysis') {
      steps {
        sh 'rm owasp* || true'
        sh 'wget "https://raw.githubusercontent.com/Blothhoondr/webapp/master/owasp-dependency-check.sh"'
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'bash owasp-dependency-check.sh'
        sh 'cat /var/lib/jenkins/owasp-dependency-check/reports/dependency-check-report.xml'
      }
    }
    
    stage ('Build') {
      steps {
       sh 'mvn clean package'
      }
    }
    
    stage ('Deploy-To-Tomcat') {
      steps {
        sshagent(['tomcat']) {
         sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@ec2-52-56-147-138.eu-west-2.compute.amazonaws.com:/opt/tomcat/latest/webapps/webapp.war'
        }
      }
    }
    
  }
}
