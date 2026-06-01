pipeline {
    agent any

    stages {

        stage('test') {
            steps {
                script {
                    println("Executing test")
                }
            }
        }

        stage('sonar analysis') {
            steps {
                script {
                    println("Executing sonar analysis")
                }
            }
        }

        stage('Scan project dependencies') {
            steps {
                script {
                    println("Scanning the project dependencies")
                }
            }
        }

        stage('docker build') {
            steps {
                script {
                    println("Building Docker Image")
                }
            }
        }

        stage('Scanning Image') {
            steps {
                script {
                    println("Scanning image using Snyk")
                }
            }
        }

        stage('Pushing Image') {
            steps {
                script {
                    println("Pushing Image to AWS ECR")
                }
            }
        }

        stage('Setting ENV for Deploy') {
            steps {
                script {

                    if (env.BRANCH_NAME == 'develop') {
                        env.ENVIRONMENT_NAME = 'dev'
                        env.AWS_EKS_CLUSTER_CREDENTIALS = 'dev-aws-eks-credentials'
                        env.EKS_CLUSTER_NAME = 'dev-eks-cluster'
                        env.REGION = 'us-east-1'

                    } else if (env.BRANCH_NAME == 'qa') {
                        env.ENVIRONMENT_NAME = 'qa'
                        env.AWS_EKS_CLUSTER_CREDENTIALS = 'qa-aws-eks-credentials'
                        env.EKS_CLUSTER_NAME = 'qa-eks-cluster'
                        env.REGION = 'us-east-1'

                    } else if (env.BRANCH_NAME == 'main') {
                        env.ENVIRONMENT_NAME = 'prod'
                        env.AWS_EKS_CLUSTER_CREDENTIALS = 'prod-aws-eks-credentials'
                        env.EKS_CLUSTER_NAME = 'prod-eks-cluster'
                        env.REGION = 'us-east-1'

                    } else {
                        error("${env.BRANCH_NAME} branch is not allowed to deploy any environment")
                    }
                }
            }
        }
        stage('Deploying to EKS') {
            steps {
                script {

                    withCredentials([
                        [$class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: env.AWS_EKS_CLUSTER_CREDENTIALS]
                    ]) {

                        sh """
                            export AWS_ACCESS_KEY_ID=\$AWS_ACCESS_KEY_ID
                            export AWS_SECRET_ACCESS_KEY=\$AWS_SECRET_ACCESS_KEY
                            export AWS_DEFAULT_REGION=${env.REGION}
                        """
                        println("aws eks update-kubeconfig --region ${env.REGION}  --name ${env.EKS_CLUSTER_NAME}")
                        println("kubectl apply -f db-namespace.yml & kubectl apply -f db-storage-class.yml")
                    }
                }
            }
        }
    }
}
