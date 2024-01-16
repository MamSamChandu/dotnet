pipeline {
  agent any
  triggers {
    pollSCM '* * * * *'
  }
 stages {
//     stage('SonarQube Analysis') {
//       steps {
//         sh '''
// 	 whoami
// 	 echo $PATH
//          echo Restore started on `date`.
//          dotnet restore panz.csproj
//          dotnet build panz.csproj -c Release
        
//         '''
//       }
//     }
    stage('Dotnet Publish') {
      steps {
        sh 'dotnet publish panz.csproj -c Release'
      }   
    }
   stage('Docker build and push') {
      steps {
        sh '''
         whoami
	 echo $access_key
	 echo $secret_key
         aws configure set aws_access_key_id $access_key
         aws configure set aws_secret_access_key $secret_key
         aws configure set default.region ap-northeast-2
         DOCKER_LOGIN_PASSWORD=$(aws ecr get-login-password  --region ap-northeast-2)
         docker login -u AWS -p $DOCKER_LOGIN_PASSWORD 645624701308.dkr.ecr.ap-northeast-2.amazonaws.com/my-ecr
	 docker build -t 645624701308.dkr.ecr.ap-northeast-2.amazonaws.com/my-ecr:SAMPLE-PROJECT-${BUILD_NUMBER} .
	 docker push 645624701308.dkr.ecr.ap-northeast-2.amazonaws.com/my-ecr:SAMPLE-PROJECT-${BUILD_NUMBER}
          
	  '''
     }   
   } 
	 
    stage('Deploy  K8'){
            steps {
                 sh '''
                  export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                  export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                  export AWS_DEFAULT_REGION=ap-south-1
		  chmod +x changebuildnumber.sh
                  ./changebuildnumber.sh $BUILD_NUMBER
                  aws eks --region ap-northeast-2 update-kubeconfig --name my-cluster
                  cat deployment.yml
                  kubectl apply -f  deployment-new.yml
                  kubectl get pods 
                  '''

            }
        }
 
//     stage('ecs deploy') {
//       steps {
//         sh '''
//           chmod +x changebuildnumber.sh
//           ./changebuildnumber.sh $BUILD_NUMBER
// 	  sh -x ecs-auto.sh
//           '''
//      }    
//     }
}
// post {
//     failure {
//         mail to: 'unsolveddevops@gmail.com',
//              subject: "Failed Pipeline: ${BUILD_NUMBER}",
//              body: "Something is wrong with ${env.BUILD_URL}"
//     }
//      success {
//         mail to: 'unsolveddevops@gmail.com',
//              subject: "successful Pipeline:  ${env.BUILD_NUMBER}",
//              body: "Your pipeline is success ${env.BUILD_URL}"
//     }
// }

 }
