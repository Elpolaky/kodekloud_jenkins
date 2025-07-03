pipeline {
    agent any

    stages {


        stage('Setup') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'userData', usernameVariable: "myuser", passwordVariable: "mypassword")]) {

                    sh '''
                    echo user is : ${myuser}
                    echo password is : ${mypassword}
                    '''
                }
                sh "apt install python3-xyz"
                sh "pip install -r requirements.txt"
            
            }
        }
        stage('Test') {
            steps {
                sh "pytest"
                
            }
        }    
        stage('Deployment') {
            input {
                message "Do you want to proceed further?"
                ok "Yes"
            }
            steps {
                echo "Running Deployment"
                
            }
        } 
        
            
    }
}