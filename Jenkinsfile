pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-1:202051054'
       appRegistry = "775141445286.dkr.ecr.us-east-1.amazonaws.com/capstoneecr"
       capstoneRegistry = "https://775141445286.dkr.ecr.us-east-1.amazonaws.com"
       cluster = "Aec_Capstone"
        service = "202051054_service"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/chandrachood932c/Capstone_wpage'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '202051054', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
