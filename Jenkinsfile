pipeline {
    agent any

    environment {
        EC2_INSTANCE_IP = credentials('EC2_IP') // Credential ID for EC2 instance IP
        SSH_KEY = credentials('STFC-SSH_ID') // Credential ID for SSH private key
    }

    stages {
        stage('Clone Public Repo')
        {
            steps {
                git branch: 'main',
                    url: 'https://github.com/NithishNithi/STFC-TestServer'
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
