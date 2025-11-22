#!/usr/bin.env groovy

pipeline {   
    agent any
    stages {
        stage("test") {
            steps {
                script {
                    echo "Testing the application..."

                }
            }
        }
        stage("build") {
            steps {
                script {
                    echo "Building the application..."
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                sshagent(['kelz-aws-ssh']) {
                    def dockerCmd = 'docker run -p 3000:3080 -d kelz107/nana-projects:react-node-aws'
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@15.223.230.218 ${dockerCmd}"
                    
                }
                }
            }
        }               
    }
} 
