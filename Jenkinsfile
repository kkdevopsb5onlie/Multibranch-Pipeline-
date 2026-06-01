pipeline {
  agent any 
  stages {
    stage ('test') {
      steps {
        script {
          println("Execting test")
        }
      }
    }
    stage ('sonar analysis') {
      step {
        script {
          println("Executing sonar analysis")
        }
      }
    }
    stage ('Scan project dependecies') {
      steps {
        script {
          println("Scanning the project depedencies") 
        }
      }
    }
    statge ('docker build') { 
       steps {
         script {
           script {
             println("Buiding Image") 
           }
         }
       }
      stage ('Scaning Image') {
        steps {
          script {
            println("Scanning image by using Snyk") 
          }
        }
      }
      stage ('Pushing Image') {
        steps {
          script {
            println("Pushing Image to aws ECR") 
          }
        }
      }
      stage ('Setting of ENV for Deploy'){
        steps {
          script {
            if(env.BRANCH_NAME == 'develop') {
               env.ENVIRONMENT_NAME = 'dev'
               env.AWS_EKS_CLUSTER_CREDENTIALS = 'dev-aws-eks-credentials'
               env.EKS_CLSUTER_NAME = 'dev-eks-cluster'
               env.REGION = 'us-east-1'
            } else if (env.BRANCH_NAME == 'qa') {
               env.ENVIRONMENT_NAME = 'qa'
               env.AWS_EKS_CLUSTER_CREDENTIALS = 'qa-aws-eks-credentials'
               env.EKS_CLSUTER_NAME = 'qa-eks-cluster'
               env.REGION = 'us-east-1'
            } else if (env.BRANCH_NAME == 'main') {
               env.ENVIRONMENT_NAME = 'prod'
               env.AWS_EKS_CLUSTER_CREDENTIALS = 'prod-aws-eks-credentials'
               env.EKS_CLSUTER_NAME = 'prod-eks-cluster'
               env.REGION = 'us-east-1'
            } else {
              error("${env.BRANCH_NAME} branch is not allowed to deploy any environment")
            }
          }
        }
      }
      stage ('deploying to EKS') {
        steps {
          script {
             withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: "${env.AWS_EKS_CLUSTER_CREDENTIAL}"]]) {
                        sh """
                            export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                            export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                            export AWS_DEFAULT_REGION="${env.REGION}"
                            echo "aws eks update-kubeconfig --region ${AWS_DEFAULT_REGION) --name ${env.EKS_CLSUTER_NAME}"
                       """
                      
                      # Apply Kubernetes manifests
                      echo "kubectl apply -f db-namespace.yml \
                      kubectl apply -f db-storage-class.yml \
                      kubectl apply -f db-pv.yml \
                      kubectl apply -f db-pvc.yml \
                      kubectl apply -f db-secrets.yml \
                      kubectl apply -f db-deployment.yml \
                      kubectl apply -f db-service.yml"
                    }
                   }
                 }
               }
            }
        }
            
'
