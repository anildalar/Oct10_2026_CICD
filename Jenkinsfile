
pipeline{
  agent any
  stages{
    stage(''' Stage 1 - Basic Setup '''){
      steps{
        sh '''
          apt update -y
          apt upgrade -y
          apt install sudo -y
          apt install docker.io docker-compose -y
          service docker start
          service docker status
        '''
      }
    }
    stage("Image Build and PUsh"){
      steps{
         withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
              sh 'docker image build -t oklabs/myimage:t${BUILD_NUMBER} -f Dockerfile.myapp .'
              sh "echo $PASS | docker login -u $USER --password-stdin"
              sh 'docker image push oklabs/myimage:t${BUILD_NUMBER}'
          }
      }
    }
  }
  
}
