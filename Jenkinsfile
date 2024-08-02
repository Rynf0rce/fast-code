pipeline {
    agent any
    environment{
        GITNAME = 'Rynf0rce'
        GITEMAIL = 'speedcore300@daum.net'
        GITWEBADD = 'https://github.com/Rynf0rce/fast-code.git'
        GITSSHADD = 'git@github.com:Rynf0rce/depolyment2.git'
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
                 withDockerRegistry(credentialsId: DOCKERCREDENTIAL, url: '') {
                                    sh "docker push ${DOCKERHUB}:${currentBuild.number}"
                                    sh "docker push ${DOCKERHUB}:latest"
                 }
            }
            post {
                // 성공 실패에 관계 없이 로컬에 있는 도커 이미지는 삭제
                failure {
                    sh "docker image rm -f ${DOCKERHUB}:${currentBuild.number}"
                    sh "docker image rm -f ${DOCKERHUB}:latest"
                    sh "echo Docker image push failed"
                }
                success {
                    sh "docker image rm -f ${DOCKERHUB}:${currentBuild.number}"
                    sh "docker image rm -f ${DOCKERHUB}:latest"
                    sh "echo Docker image push success"
                }
            }
        }
        stage('EKS manifest file update') {
            steps {
                git credentialsId: GITCREDENTIAL, url: GITSSHADD, branch: 'main'
                sh "git config --global user.email ${GITEMAIL}"
                sh "git config --global user.name ${GITNAME}"
                sh "sed -i 's@${DOCKERHUB}:.*@${DOCKERHUB}:${currentBuild.number}@g' fast.yml"

                sh "git add ."
                sh "git branch -M main"
                sh "git commit -m 'fixed tag ${currentBuild.number}'"
                sh "git remote remove origin"
                sh "git remote add origin ${GITSSHADD}"
                sh "git push origin main"
            }
            post {
                failure {
                    sh "echo EKS manifest file update failed"
                }
                success {
                    sh "echo EKS manifest file update success"
                }
            }
        }
        stage('Checkout Githun') {
            steps {
                SlackSend (
                    channel: '#drs',
                    color: '#FFFF00'
                    message: "STARED:
                    ${currentBuild.number}"

                )
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