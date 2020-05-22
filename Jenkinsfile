pipeline {

    agent none

    environment {

        NODE_ENV="development"
        AWS_ACCESS_KEY=""
        AWS_SECRET_ACCESS_KEY=""
        AWS_SDK_LOAD_CONFIG="0"
        BUCKET_NAME="app-digital"
        REGION="us-east-1" 
        PERMISSION=""
        ACCEPTED_FILE_FORMATS_ARRAY=""
        VERSION="1.0.0"
    }


    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    triggers {
        cron('@daily')
    }

    stages{
        stage("Build, Test and Push Docker Image") {
            agent {  
                node {
                    label 'master'
                }
            }
            stages {

                stage('Clone repository') {
                    steps {
                        script {
                            if(env.GIT_BRANCH=='origin/dev'){
                                checkout scm
                            }
                            sh('printenv | sort')
                            echo "My branch is: ${env.GIT_BRANCH}"
                        }
                    }
                }
                stage('Build image'){       
                    steps {
                        script {
                            print "Environment will be : ${env.NODE_ENV}"
                            docker.build("digitalhouse-devops:latest")
                        }
                    }
                }

                stage('Test image') {
                    steps {
                        script {

                            docker.image("digitalhouse-devops:latest").withRun('-p 8030:3000') { c ->
                                sh 'docker ps'
                                sh 'sleep 10'
                                sh 'curl http://127.0.0.1:8030/api/v1/healthcheck'
                                
                            }
                    
                        }
                    }
                }

                stage('Docker push') {
                    steps {
                        echo 'Push latest para AWS ECR'
                        script {
                            docker.withRegistry('https://682647774837.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:ecr-key') {
                                docker.image('digitalhouse-devops').push()
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy to Homolog') {
            agent {  
                node {
                    label 'homolog'
                }
            }

            steps { 
                script {
                    if(env.GIT_BRANCH=='origin/homolog'){
 
                        docker.withRegistry('https://682647774837.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:ecr-key') {
                            docker.image('digitalhouse-devops').pull()
                        }

                        echo 'Deploy para Desenvolvimento'
                        sh "hostname"
                        catchError{
                            sh "docker stop app1"
                            sh "docker rm app1"    
                        }
                        //sh "docker run -d --name app1 -p 8030:3000 933273154934.dkr.ecr.us-east-1.amazonaws.com/digitalhouse-devops:latest"
                        withCredentials([[$class:'AmazonWebServicesCredentialsBinding' 
                            , credentialsId: 'dh-lemniscata-devops-homolog']]) {
                        sh "docker run -d --name app1 -p 8030:3000 -e NODE_ENV=homolog -e AWS_ACCESS_KEY=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY -e BUCKET_NAME=dh-lemniscata-devops-homolog 682647774837.dkr.ecr.us-east-1.amazonaws.com/digitalhouse-devops:latest"
                        }
                        
                        sh "docker ps"
                        sh 'sleep 10'
                        sh 'curl ec2-3-81-72-146.compute-1.amazonaws.com:8030/api/v1/healthcheck'

                    } else {
                        echo "Current Job is "prod", skipping step."
                    }
                }
            }

        }

        stage('Deploy to Producao') {
            agent {  
                node {
                    label 'prod'
                }
            }

            steps { 
                script {
                    if(env.GIT_BRANCH=='master'){
 
                        environment {

                            NODE_ENV="prod"
                            AWS_ACCESS_KEY=""
                            AWS_SECRET_ACCESS_KEY=""
                            AWS_SDK_LOAD_CONFIG="0"
                            BUCKET_NAME="dh-lemniscata-devops-prod"
                            REGION="us-east-1" 
                            PERMISSION=""
                            ACCEPTED_FILE_FORMATS_ARRAY=""
                        }


                        docker.withRegistry('https://682647774837.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:ecr-key') {
                            docker.image('digitalhouse-devops').pull()
                        }

                        echo 'Deploy para Producao'
                        sh "hostname"
                        sh "docker stop app1"
                        sh "docker rm app1"
                        //sh "docker run -d --name app1 -p 8030:3000 933273154934.dkr.ecr.us-east-1.amazonaws.com/digitalhouse-devops:latest"
                        withCredentials([[$class:'AmazonWebServicesCredentialsBinding' 
                            , credentialsId: 'dh-lemniscata-devops-prod']]) {
                          sh "docker run -d --name app1 -p 3.208.92.64:8030:3000 -e NODE_ENV=prod -e AWS_ACCESS_KEY=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY -e BUCKET_NAME=dh-lemniscata-devops-prod https://682647774837.dkr.ecr.us-east-1.amazonaws.com/digitalhouse-devops:latest"
                        }
                        sh "docker ps"
                        sh 'sleep 10'
                        sh 'curl http://ec2-54-174-111-243.compute-1.amazonaws.com:8030/api/v1/healthcheck'

                    } else {
                        echo "Current Job is "homolog", skipping step."
                    }
                }
            }

        }

    }
}