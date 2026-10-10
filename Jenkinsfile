
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
    stage('Test Production SSH') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'prodserver103',
                usernameVariable: 'SSH_USER',
                passwordVariable: 'SSH_PASS'
            )
        ]) {
            sh '''
                set +x
                sshpass -V
            '''
        }
    }
}
    stage(' Deploy on Production Server '){
      steps{
        // 1. Login to Production Server
        
        // 2. Pull(Download) the image 
        // 3. Run the container
        /*
            script {
                    def dockerCmd = 'docker run  -p 9000:80 -d oklabs/myimage:latest'
                    sshagent(['prodserver103']) {
                        sh "ssh root@103.216.171.220 ${dockerCmd}"
                    }
              }
              */
        withCredentials([
            usernamePassword(
                credentialsId: 'prodserver103',
                usernameVariable: 'SSH_USER',
                passwordVariable: 'SSHPASS'
            )
        ]) {
            sh '''
                set +x

                sshpass -e ssh \
                    -o StrictHostKeyChecking=accept-new \
                    "$SSH_USER@103.216.171.220" \
                    "docker pull oklabs/myimage:t${BUILD_NUMBER} &&
                     (docker rm -f myapp >/dev/null 2>&1 || true) &&
                     docker run -d \
                       --name myapp \
                       --restart unless-stopped \
                       -p 9000:80 \
                       oklabs/myimage:t${BUILD_NUMBER}"
            '''
        }
        
      }
    }
  }
  
}
