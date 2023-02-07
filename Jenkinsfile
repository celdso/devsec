pipeline {
  agent any 
  tools {
    maven 'maven'
  }
  stages {
    stage ('Initialize') {
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
       sh 'docker run gesellix/trufflehog --json https://github.com/celdso/devsec.git > trufflehog'
       sh 'cat trufflehog'
     }
   }
    stage ('Source Composition Analysis') {
      steps {
        sh 'rm owasp* || true'
        sh 'wget "https://github.com/celdso/devsec/blob/master/owasp_dependency_check.sh"'
        sh 'chmod +x owasp_dependency_check.sh'
        sh 'bash owasp_dependency_check.sh'
        sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
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
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@54.175.232.61:/opt/tomcat/webapps/webapp.war'
              }      
           }       
    }    

    }
}
