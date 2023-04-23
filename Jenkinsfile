pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-1:capstone_kiran'
       appRegistry = "846925906451.dkr.ecr.us-east-1.amazonaws.com/capstoneproject"
       capstoneRegistry = "https://846925906451.dkr.ecr.us-east-1.amazonaws.com"
       cluster = "202051170_capstone"
       service = "kiran_service"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/nanibangaram9/website.git'
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
            withAWS(credentials: 'capstone_kiran', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
