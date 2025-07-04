pipeline {
    agent any
    parameters{
        booleanParam(name: 'test' , defaultValue: true )

    }
    environment{
        SERVER_IP = credentials('server_ip')
    }
    stages {
        // stage('Setup') {
        //     steps {
        //         withCredentials([usernamePassword(credentialsId: 'userData', usernameVariable: "myuser", passwordVariable: "mypassword")]) {

        //             sh '''
        //             echo user is : ${myuser}
        //             echo password is : ${mypassword}
        //             '''
        //         }
        //         // sh "sudo apt update"
        //         // sh "sudo apt install python3-xyz"
        //         // sh "pip install -r requirements.txt"
            
        //     }
        // }

        stage('Check Environment') {
            steps {
                sh '''
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
            when{
                expression { params.test == true }
               
            }
            steps {
                //echo "sh 'pytest'"
                echo "Running Tests"

            }
        }    
        stage('prepare Code'){
            steps {
                echo "Preparing Code"
                sh "zip -r myapp.zip ./* -x '**.git**'"
                sh "ls -lart"
            }
        }
        stage('Deployment') {
            input {
                message " Deploy or not ?????"
                ok "Yes"
            }
            steps {
                echo "Running Deployment"
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key', keyFileVariable: 'MY_SSH_KEY', usernameVariable: 'username')]) {
                    sh '''
                    scp -i $MY_SSH_KEY -o StrictHostKeyChecking=no myapp.zip ${username}@${SERVER_IP}:/home/ec2-user/
                    ssh -i $MY_SSH_KEY -o StrictHostKeyChecking=no ${username}@${SERVER_IP} << EOF
                    unzip -o /home/ec2-user/myapp.zip -d /home/ec2-user/app/
                    source app/venv/bin/activate
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