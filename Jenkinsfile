#!/bin/bash
pipeline {
    agent any
    tools {
    maven 'Maven'
    }
    environment {
        REPOSITORY = "https://github.com/ZhiTingXin/demo.git"
        SERVICE_DIR = "demo"
    }
    stages {
        stage('pull code') {
            steps {
                echo "start fetch code from git:${REPOSITORY}"
                deleteDir()
                git "${REPOSITORY}"
                script {
                    time = sh(returnStdout: true, script: 'date "+%Y%m%d%H%M"').trim()
                    git_version = sh(returnStdout: true, script: 'git log -1 --pretty=format:"%h"').trim()
                    build_tag = time + git_version
                }
            }
        }
       

        stage('build maven') {
            steps {
                echo "begin pkg"
                sh "mvn sonar:sonar -Dsonar.host.url=http://172.17.0.5:9000 -Dsonar.login=ea73c1fc82c1ec062f7e9ee2bb999a00c17f02b9"
                sh "ls -a"
                sh "mvn -v"
                sh "mvn -U -am clean package"
                
            }
        }

        stage('build docker') {
            steps {
                echo "begin build image with docker"
                sh "ls -l"
                sh "docker build -t ${SERVICE_DIR}:${build_tag} ."
            }
        }
        stage('deploy'){
            steps {
                echo "run container"
                sh "docker images"
                sh "docker run --name hello -d -v /home/jenkins/springBootDocker/logs:/log -p 8088:8080 ${SERVICE_DIR}:${build_tag}"
            }
        }
    }
}
