pipeline{

    agent any 
    
    tools {
        maven 'maven'
    }

    stages{

        stage('Git Checkout'){
           
            steps{

                script{
                 
                 git branch: 'dev', url: 'https://github.com/rameshkumarvermagithub/taxi-booking.git'

                }
            }
        }
        stage('Unit Test'){

             steps{

              script{
                   
                   sh 'mvn test'

                }
             }
        }
        stage('Integration Test'){

             steps{

              script{
                   
                   sh 'mvn verify -DskipUnitTests'

                }
             }
        }
        stage('Static Code Analysis'){

             steps{

              script{
                   
                  withSonarQubeEnv(credentialsId: 'sonar') {
                     
                     sh 'mvn clean package sonar:sonar'
                  }
                }
             }
        }
        stage('Quality Gate status Check'){

             steps{

              script{
                   
                   waitForQualityGate abortPipeline: false, credentialsId: 'sonar'

                }
             }
        }
        stage('Maven Build'){

             steps{

              script{
                   
                   sh 'mvn clean install'

                }
             }
        }
        // stage('Docker image Building'){

        //      steps{

        //       script{
                   
        //           sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
        //           sh 'docker image tag $JOB_NAME:v1.$BUILD_ID rameshkumarverma/$JOB_NAME:v1.$BUILD_ID'
        //           sh 'docker image tag $JOB_NAME:v1.$BUILD_ID rameshkumarverma/$JOB_NAME:latest'

        //         }
        //      }
        // }
        // stage('Docker image push'){

        //      steps{

        //       script{
        //           withCredentials([string(credentialsId: 'docker', variable: 'docker')]) {
                     
        //              sh 'docker login -u rameshkumarverma -p ${docker}'
        //              sh 'docker image push rameshkumarverma/$JOB_NAME:v1.$BUILD_ID'
        //              sh 'docker image push rameshkumarverma/$JOB_NAME:latest'
        //           }
        //         }
        //      }
        // }
        
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        
                        sh "docker build -t rameshkumarverma/taxi-booking ."
                        // sh "docker tag  taxi-booking rameshkumarverma/taxi-booking:latest"
                        sh "docker push rameshkumarverma/taxi-booking:latest"
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image rameshkumarverma/taxi-booking:latest > trivyimage.txt"
            }
        }
        // stage("deploy_docker"){
        //     steps{
        //         sh "docker run -d --name taxi-booking -p 8000:8000 rameshkumarverma/taxi-booking:latest"
        //     }
        // }

        stage('Deploy to kubernets'){
            steps{
                script{
                    // dir('K8S') {
                        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                                sh 'kubectl apply -f deployment.yaml'
                                sh 'kubectl apply -f service.yaml'
                        }
                    // }
                }
            }
        }

         stage(" Deploy ") {
          steps {
            script {
               echo '<--------------- Deploy Started --------------->'
               sh './deploy.sh'
               echo '<--------------- Deploy Ends --------------->'
            }
          }
        }

    }
}
