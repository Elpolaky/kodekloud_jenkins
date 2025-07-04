pipeline {
    agent any
    parameters {
        booleanParam(name: 'test', defaultValue: true)
    }
    environment {
        SERVER_IP = credentials('server_ip')
    }
    stages {
        stage('Check Environment') {
            steps {
                sh '''
                    echo ${SERVER_IP}
                    echo ">> Who am I?"
                    whoami

                    echo ">> Is sudo installed?"
                    which sudo || echo "sudo NOT FOUND"

                    echo ">> Can I run apt update?"
                    apt update || echo "apt failed"
                '''
            }
        }

        stage('Test') {
            when {
                expression { params.test == true }
            }
            steps {
                echo "Running Tests"
            }
        }

        stage('Prepare Code') {
            steps {
                echo "Preparing Code"
                sh "zip -r myapp.zip ./* -x '**.git**'"
                sh "ls -lart"
                sh "pwd"
            }
        }

        stage('Deployment') {
            steps {
                echo "Running Deployment"
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ssh-key',
                        keyFileVariable: 'MY_SSH_KEY',
                        usernameVariable: 'username'
                    )
                ]) {
                    sh """
                        echo "MY_SSH_KEY is: \$MY_SSH_KEY"
                        ls -l \$MY_SSH_KEY

                        echo "Copying to server: \$SERVER_IP"
                        mkdir -p ~/.ssh
                        ssh-keyscan -H \$SERVER_IP >> ~/.ssh/known_hosts

                        scp -i \$MY_SSH_KEY -o StrictHostKeyChecking=no myapp.zip \${username}@\${SERVER_IP}:/home/ubuntu/

                        ssh -i \$MY_SSH_KEY -o StrictHostKeyChecking=no \${username}@\${SERVER_IP} << 'EOF'
set -e

unzip -o /home/ubuntu/myapp.zip -d /home/ubuntu/app/

cd /home/ubuntu/app/
python3 -m venv venv
source venv/bin/activate

if [ -f requirements.txt ]; then
    pip install -r requirements.txt
else
    echo "requirements.txt not found!"
    exit 1
fi

sudo systemctl restart flaskapp.service
EOF
                    """
                }
            }
        }
    }
}
