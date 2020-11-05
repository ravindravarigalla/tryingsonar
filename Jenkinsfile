pipeline {

  environment {
    PROJECT = "	still-smithy-279711"
    APP_NAME = "sample"
    FE_SVC_NAME = "${APP_NAME}"
    CLUSTER = "cluster-1"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:latest"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  
  containers:
  - name: go
    image: golang:1.15-alpine
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/google.com/cloudsdktool/cloud-sdk:latest
    command:
    - cat
    tty: true
  - name: helm
    image: nginx
    command:
    - cat
    tty: true
    
"""
}
  }
  stages {
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh """
             sonar-scanner \
              -Dsonar.projectKey=testing \
              -Dsonar.sources=. \
              -Dsonar.host.url=http://35.239.36.86:9000 \
              -Dsonar.login=10a603f6f4ac9eca1c7d03943057ced680a299b2
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }
  }  
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "#gcloud auth list"
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t  gcr.io/my-project-suri-279708/go . "
          sh "#gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project still-smithy-279711"
        }
      }
    }
  }
}

