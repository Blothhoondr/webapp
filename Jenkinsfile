pipeline {
  agent any 
  tools {
    maven 'maven'
  }

  stages {
    stage ('Intialize') {
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
        sh 'docker run gesellix/trufflehog --json https://https://github.com/Blothhoondr/webapp.git > trufflehog'
        sh 'cat trufflehog'
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
