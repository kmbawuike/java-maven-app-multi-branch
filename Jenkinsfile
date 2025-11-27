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

    stages {
        stage('increment version') {
            steps {
                script {
                    echo 'incrementing app version...'
                    sh 'mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "kelz107/nana-projects${version}-${BUILD_NUMBER}"
                }
            }
        }

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
                    echo 'deploying docker image to EC2...'

                    def shellCmd = "bash ./server-cmds.sh ${env.IMAGE_NAME}"
                    def ec2Instance = "ec2-user@15.223.209.219"

                    sshagent(['aws-ec2-ssh']) {
                        sh "scp server-cmds.sh ${ec2Instance}:/home/ec2-user"
                        sh "scp docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
                    }
                }
            }               
        }

        stage("commit version update") {
            steps {
                script {
                    sshagent(['git-ssh']) {
                        sh 'git remote set-url origin git@github.com:kmbawuike/java-maven-app-multi-branch.git'
                        sh 'git config --global user.email "jenkins222@gmail.com"'
                        sh 'git config --global user.name "jenkins"'
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:main'
                    }
                }
            }               
        } 
    }
}