pipeline {
    agent any
    tools{
        maven "Maven3.8.7"
    }
     environment {
        registry = "961565152773.dkr.ecr.us-west-1.amazonaws.com/mani"
    }
   
    stages {
          stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Manideepthaduri/jenkins-ECR.git'
            }
        }
          stage('Build the code') {
            steps {
                sh 'mvn clean install'
            }
        }   
           stage('Building image') {
             steps{
                  script {
                   dockerImage = docker.build registry
                   }
      }
           }
    
            stage('Pushing to ECR') {
             steps{  
                  script {
               withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'ECR', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
    sh 'aws ecr get-login-password --region us-west-1 | docker login --username AWS --password-stdin 961565152773.dkr.ecr.us-west-1.amazonaws.com/mani'
     sh 'docker push 961565152773.dkr.ecr.us-west-1.amazonaws.com/mani'
}

}
                  }
            }
             stage('stop previous containers') {
               steps {
            sh 'docker ps -f name=mani -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mani -q | xargs -r docker container rm'
         }
       }
            stage('Docker Run') {
              steps{
                   script {
                sh 'docker run -d -p 8096:5000 --rm --name mani 961565152773.dkr.ecr.us-west-1.amazonaws.com/mani:latest'     
      }
    }
        }
    }
  }
