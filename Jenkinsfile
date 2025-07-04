pipeline {
    agent any
    parameters{
        booleanParam(name: 'test' , defaultValue: true )
    }
    stages {


        stage('Setup') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'userData', usernameVariable: "myuser", passwordVariable: "mypassword")]) {

                    sh '''
                    echo user is : ${myuser}
                    echo password is : ${mypassword}
                    '''
                }
                // sh "sudo apt update"
                // sh "sudo apt install python3-xyz"
                // sh "pip install -r requirements.txt"
            
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
        stage('Deployment') {
            input {
                message " Deploy or not ?????"
                ok "Yes"
            }
            steps {
                // echo 'input {
                //     message "Do you want to proceed further?"
                //     ok "Yes"
                // }'
                echo "Running Deployment"
                
            }
        } 
        
            
    }
}