pipeline {
  agent any 
  tools {
    go 'go'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    #echo "PATH = ${PATH}"
                    #echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    
    stage ('Check-Git-Secrets') {
      steps {
        sh '#rm trufflehog || true'
        sh '#docker run gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehog'
        sh '#cat trufflehog'
      }
    }
    
    stage ('Source Composition Analysis') {
      steps {
         sh '#rm owasp* || true'
         sh '#wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh '#chmod +x owasp-dependency-check.sh'
         sh '#bash owasp-dependency-check.sh'
         sh '#cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
    
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh """
            sonar-scanner \
              -Dsonar.projectKey=frontend \
              -Dsonar.sources=. \
              -Dsonar.host.url=http://35.239.36.86:9000 \
              -Dsonar.login=testing
          sh 'cat target/sonar/report-task.txt'
        }
      }
    
  }
}
