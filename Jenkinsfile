#!/usr/bin/env groovy

pipeline {
    agent any

        parameters {
        string(name: 'environment', defaultValue: 'default', description: 'Workspace/environment file to use for deployment')
        string(name: 'version', defaultValue: '', description: 'Version variable to pass to Terraform')
        password (name: 'AWS_ACCESS_KEY_ID')
        password (name: 'AWS_SECRET_ACCESS_KEY')
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    }

    environment {
        IMAGE_NAME = 'nanajanashia/demo-app:java-maven-2.0'
  
    }
    stages {
        stage('build app') {
            steps {
               script {
                  echo 'building application jar...'
                
               }
            }
        }
        stage('build image') {
            steps {
                script {
                   echo 'building docker image...'
                  
                }
            }
        }
        stage('provision server') {
            environment {
                TF_VAR_env_prefix = 'test'
                AWS_ACCESS_KEY_ID     = "${params.AWS_ACCESS_KEY_ID}"
                AWS_SECRET_ACCESS_KEY = "${params.AWS_SECRET_ACCESS_KEY}"
            }
            steps {
                script {
                    dir('terraform') {
                        sh "terraform init"
                        sh "terraform apply --auto-approve"
                        EC2_PUBLIC_IP = sh(
                            script: "terraform output ec2_public_ip",
                            returnStdout: true
                        ).trim()
                    }
                }
            }
        }
        stage('deploy') {
            environment {
                //DOCKER_CREDS = credentials('docker-hub-repo')
                TF_VAR_env_prefix = 'test'
            }
            steps {
                script {
                   echo "waiting for EC2 server to initialize" 
                   sleep(time: 90, unit: "SECONDS") 

                   echo 'deploying docker image to EC2...'
                   echo "${EC2_PUBLIC_IP}"

                //    def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME} ${DOCKER_CREDS_USR} ${DOCKER_CREDS_PSW}"
                //    def ec2Instance = "ec2-user@${EC2_PUBLIC_IP}"

                //    sshagent(['server-ssh-key']) {
                //        sh "scp -o StrictHostKeyChecking=no server-cmds.sh ${ec2Instance}:/home/ec2-user"
                //        sh "scp -o StrictHostKeyChecking=no docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                //        sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
                //    }
                }
            }
        }
    }
}
