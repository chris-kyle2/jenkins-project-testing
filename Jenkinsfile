pipeline {
    agent any
    environment{
        SERVER_IP = credentials('prod-server-ip')
    }
    options { skipDefaultCheckout() }
    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/chris-kyle2/jenkins-project-testing.git', branch: 'main'
                sh "echo 'Successfully checked out repository' "
                sh "ls -ltr"
            }
        }

        stage('Setup') {
            steps {
        sh '''
        python3 -m venv venv
        . venv/bin/activate
        pip install --upgrade pip
        pip install -r requirements.txt
        '''
            }
        }

        stage('Test') {
            steps {
                 sh './venv/bin/pytest'
            }
        }
        stage('package-code'){
            steps{
                sh " zip -r myapp.zip ./* -x '*.git*' "
                sh "ls -lart"
            }
        }
        stage('Deploy to prod stage'){
            steps{
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key',keyFileVariable: 'MY_SSH_KEY',usernameVariable: 'username')]){
                    sh ''' 
                        scp -i $MY_SSH_KEY -o StrictHostKeyChecking=no  myapp.zip ${username}@${SERVER_IP}:/home/ec2-user
                        ssh -i $MY_SSH_KEY -o StrictHostKeyChecking=no  ${username}@${SERVER_IP} << EOF
                        unzip -o /home/ec2-user/myapp.zip -d  /home/ec2-user/app
                        sudo yum install python3 python3-pip -y
                        rm -rf /home/ec2-user/app/venv
                        python3 -m venv /home/ec2-user/app/venv
                        source /home/ec2-user/app/venv/bin/activate
                        pip install --upgrade pip
                        cd /home/ec2-user/app
                        pip install -r requirements.txt
                        sudo systemctl restart flaskapp.service
EOF


                    ''' 
                }
            }
        }
    }
}
