pipeline {
    agent any
    environment {
        IMAGE_NAME = 'nkposa/jenkins-flaskapp'
        IMAGE_TAG = "${IMAGE_NAME}:${env.GIT_COMMIT}"
    }
    stages {
        stage('checkout') {
            steps {
                git url: 'https://github.com/Nkposa/project2.git', branch: 'main'
                sh "ls -ltr"
            }
        }
        // stage('setup') {
        //     steps {
        //         sh "sudo pip install -r requirements.txt"
        //     }
        // }
        // stage('test') {
        //     steps {
        //         sh 'pytest'
        //         sh 'whoami'
        //     }
        // }
        stage('docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo ${PASSWORD} | docker login -u ${USERNAME} --password-stdin'
                }
                sh 'echo login_successfully'
            }
        }
        stage('docker build image') {
            steps {
                sh 'docker build -t ${IMAGE_TAG} .'
                sh 'echo image-build-successfully'
                sh 'docker images'
            }
        }
        stage('docker push') {
            steps {
                sh 'docker push ${IMAGE_TAG}'
                sh 'echo Image_pushed'
            }
        }
    }
}