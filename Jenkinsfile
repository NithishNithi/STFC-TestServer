pipeline {
    agent any

    environment {
        EC2_INSTANCE_IP = credentials('EC2_IP') // Credential ID for EC2 instance IP
        SSH_KEY = credentials('STFC-SSH_ID') // Credential ID for SSH private key
    }

    stages {
        stage('Fetch Code') {
            steps {
                // Clone your GitHub repository
                git credentialsId: 'github-credentials', url: 'https://github.com/NithishNithi/STFC-TestServer.git'
            }
        }

        stage('Deploy') {
            steps {
                // Copy the 'STFC-TestServer' folder to EC2 instance
                script {
                    sshCommand = "scp -i ${SSH_KEY} -r STFC-TestServer ec2-user@${EC2_INSTANCE_IP}:~/"
                    sh sshCommand
                }

                // SSH into EC2 instance and run the binary file
                script {
                    sshCommand = "ssh -i ${SSH_KEY} ec2-user@${EC2_INSTANCE_IP} 'cd ~/STFC-TestServer && ./stfc &'"
                    sh sshCommand
                }

                // Check running process on EC2 instance (optional)
                script {
                    sshCommand = "ssh -i ${SSH_KEY} ec2-user@${EC2_INSTANCE_IP} 'pgrep -fl stfc'"
                    result = sh(script: sshCommand, returnStdout: true).trim()
                    echo "Running process: ${result}"
                }
            }
        }
    }
}
