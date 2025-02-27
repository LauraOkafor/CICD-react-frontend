pipeline {
    agent any
    
    triggers {
        githubPush()
    }
    
    tools {
        nodejs 'nodejenkins'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'jenkins', url: 'https://github.com/LauraOkafor/CICD-react-frontend.git'
            }
        }
        
        stage('Setup Node.js') {
            steps {
                sh 'node --version'
                sh 'npm --version'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Build Project') {
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'dist/**/*', fingerprint: true
            }
        }
        
        stage('Deploy') {
            steps {
                sshagent(credentials: ['EC2_SSH_KEY']) {
                    sh '''
                        mkdir -p ~/.ssh
                        ssh-keyscan -H 51.20.93.71 >> ~/.ssh/known_hosts
                        scp -r dist/* ubuntu@51.20.93.71:/tmp/react-app
                        
                        ssh -o StrictHostKeyChecking=no ubuntu@51.20.93.71 "
                            sudo apt update && sudo apt upgrade -y
                            echo "Successful Cache Update"
                            sudo apt install nginx -y
                            echo "Successful Nginx Installation"
                            sudo systemctl start nginx
                            sudo cp -r /tmp/react-app/* /var/www/html
                            sudo systemctl restart nginx
                        "    
                    '''
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
