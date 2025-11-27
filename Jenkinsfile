#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/kmbawuike/java-jenkins-shared-library.git',
    credentialsID: 'kelz-github'
    ]
)

pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        IMAGE_NAME = 'kelz107/nana-projects:3.0'
    }
    stages {
        stage('build app') {
            steps {
                echo 'building application jar...'
                buildJar()
            }
        }
        stage('build image') {
            steps {
                script {
                    echo 'building the docker image...'
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        } 
        stage("deploy") {
            steps {
                script {
                    echo 'deploying docker image to EC2...'
                    def dockerComposeCmd = "docker-compose -f docker-compose.yaml up --detach"
                    sshagent(['aws-ec2-ssh']) {
                        sh "ssh-keyscan -H 15.223.209.219 >> ~/.ssh/known_hosts"
                        sh "scp docker-compose.yaml ec2-user@15.223.209.219:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@15.223.209.219 ${dockerComposeCmd}"
                    }
                }
            }               
        }
    }
}