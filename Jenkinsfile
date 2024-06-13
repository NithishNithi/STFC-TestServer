pipeline {
    agent any

    environment {
        EC2_INSTANCE_IP = credentials('EC2_IP') // Credential ID for EC2 instance IP
        SSH_KEY = credentials('STFC-SSH_ID') // Credential ID for SSH private key
    }

    stages {
        stage('Clone Public Repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/NithishNithi/STFC-TestServer'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh ls -lhrs
                    // Adjust path if necessary based on Jenkins workspace structure
                    sshCommand = "scp -o StrictHostKeyChecking=no -i ${SSH_KEY} -r STFC-TestServer ec2-user@${EC2_INSTANCE_IP}:/home/ec2-user"
                    
                    sh sshCommand
                }

                script {
                    // SSH into EC2 instance and run the binary file
                    sshCommand = "ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ec2-user@${EC2_INSTANCE_IP} 'cd /home/ec2-user/STFC-TestServer && ./stfc &'"
                    sh sshCommand
                }

                script {
                    // Check running process on EC2 instance (optional)
                    sshCommand = "ssh -i ${SSH_KEY} ec2-user@${EC2_INSTANCE_IP} 'pgrep -fl stfc'"
                    result = sh(script: sshCommand, returnStdout: true).trim()
                    echo "Running process: ${result}"
                }
            }
        }
    }
}
