pipeline {
    agent any
    environment{
        GITNAME = 'rynf0rce'
        GITMAIL = 'speedcore300@daum.net'
        GITWEBADD = 'https://github.com/Rynf0rce/fast-code.git'
        GITSSHADD = 'git@github.com:Rynf0rce/fast-code.git'
        GITCREDENTIAL = 'git_cre'
        DOCKERHUB = 'rynf0rce/fast'
        DOCKERCREDENTIAL = 'docker_cre'
    }
    stages {
        stage('Checkout Github') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [],
                userRemoteConfigs: [[credentialsId: GITCREDENTIAL, url: GITWEBADD]]])
            }
            post {
                failure {
                    sh "echo clone failed"
                }
                success {
                    sh "echo clone success"
                }
            }
        }

        stage('Docker image build') {
            steps {
                sh "docker build -t ${DOCKERHUB}:${currentBuild.number} ."
                sh "docker build -t ${DOCKERHUB}:latest ."
                // currentBuild.number 젠킨스가 제공하는 빌드 넘버 변수
                // rynf0rce/fast:<빌드 넘버>와 같은 이미지가 만들어 질 예정
            }
            post {
                failure {
                    sh "echo Docker image build failed"
                }
                success {
                    sh "echo Docker image build success"
                }
            }
        }

        stage('Docker image push') {
            steps {
                 withDockerRegistry(credentialsId: DOCKERHUBCREDENTIAL, url: '') {
                                    sh "docker push ${DOCKERHUB}:${currentBuild.number}"
                                    sh "docker push ${DOCKERHUB}:latest"
            }
            post {
                failure {
                    sh "echo Docker image push failed"
                }
                success {
                    sh "echo Docker image push success"
                }
            }
        }
        stage('start4') {
            steps {
                sh "echo hello jenkins!!!"
            }
            post {
                failure {
                    sh "echo failed"
                }
                success {
                    sh "echo success"
                }
            }
        }
        stage('start5') {
            steps {
                sh "echo hello jenkins!!!"
            }
            post {
                failure {
                    sh "echo failed"
                }
                success {
                    sh "echo success"
                }
            }
        }
    }
}