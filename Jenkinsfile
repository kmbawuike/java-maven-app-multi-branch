#!/usr/bin/env groovy

pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        IMAGE_NAME = 'kelz107/nana-projects:2.0'
    }
    stages {
        stage('build app') {
            steps {
                echo 'building application jar...'
                sh 'mvn --version'
                sh 'mvn package'
            }
        }
        // stage('build image') {
        //     steps {
        //         script {
        //             echo 'building the docker image...'
        //             sh "docker build -t $env.IMAGE_NAME ."
        //             withCredentials([usernamePassword(credentialsId: 'dockerhub-id', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
        //             sh "echo '${PASS}' | docker login -u '${USER}' --password-stdin"
        //             }
        //             "docker push $env.IMAGE_NAME"
        //         }
        //     }
        // } 
        // stage("deploy") {
        //     steps {
        //         script {
        //             echo 'deploying docker image to EC2...'
        //             def dockerCmd = "docker run -p 3000:3080 -d ${IMAGE_NAME}"
        //             sshagent(['kelz-aws-ssh']) {
        //                 sh "ssh -o StrictHostKeyChecking=no ec2-user@15.222.241.179 ${dockerCmd}"
        //             }
        //         }
        //     }               
        // }
    }
}
