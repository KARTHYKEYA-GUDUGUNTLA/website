pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:202051076'
       appRegistry = "015482859191.dkr.ecr.ap-northeast-1.amazonaws.com/karthy_capstone"
       capstoneRegistry = "http://015482859191.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "capstone"
        service = "capstone_service"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/KARTHYKEYA-GUDUGUNTLA/website'
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
            withAWS(credentials: '202051076', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
