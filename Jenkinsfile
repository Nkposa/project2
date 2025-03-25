pipeline {
    agent node1
    environment {
        SERVER_IP = credentials('prod-server-ip')
    }
    stages {
        stage('checkout') {
            steps {
                git url: 'https://github.com/Nkposa/project2.git', branch: 'main'
                sh "ls -ltr"
            }
        }
        stage('Setup') {
            steps {
                sh "source /home/ec2-user/app/venv/bin/activate"
                sh "sudo pip install -r requirements.txt"
            }
        }
        stage('Test') {
            steps {
                sh "sudo pytest"
                sh "whoami"
            }
        }
        stage('package file') {
            steps {
                sh "sudo zip -r mainapp.zip ./* -x '*.git*'"
                sh "sudo ls -lart"
            }
        }
        stage('Deploy to Prod') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key', keyFileVariable: 'MY_SSH_KEY', usernameVariable: 'username')]) {
                    sh '''
                    scp -i $MY_SSH_KEY -o StrictHostKeyChecking=no myapp.zip ${username}@${SERVER_IP}:/home/ec2-user/
                    ssh -i $MY_SSH_KEY -o StrictHostKeyChecking=no ${username}@${SERVER_IP} << EOF
                    unzip -o /home/ec2-user/myapp.zip -d /home/ec2-user/app/
                    source /home/ec2-user/app/venv/bin/activate
                    cd /home/ec2-user/app/
                    pip install -r requirements.txt
                    sudo systemctl restart flaskapp.service
                    EOF
                    '''
                }
            }
        }
    }
}
