pipeline {
    agent any

    environment {
        SSH_CREDENTIALS_ID = 'STFC-SSH_ID' // Use the ID of the SSH credentials configured in Jenkins
        GIT_SSH_CREDENTIALS_ID = 'GITHUB-SSH' // Replace with the ID of your Git SSH credentials
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Add GitHub to known hosts
                    sh 'ssh-keyscan github.com >> ~/.ssh/known_hosts'

                    sshagent(credentials: [GIT_SSH_CREDENTIALS_ID]) {
                        sh 'git clone git@github.com:NithishNithi/STFC-TestServer.git'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'echo "Building..."' // Replace with your actual build commands
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Use withCredentials block for EC2_IP
                    withCredentials([string(credentialsId: 'EC2_IP', variable: 'EC2_IP')]) {
                        // Log the EC2_IP to ensure it's retrieved correctly
                        echo "EC2_IP is: ${EC2_IP}"

                        sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                            // Copy files to EC2
                            sh """
                                scp -o StrictHostKeyChecking=no -r STFC-TestServer ec2-user@${EC2_IP}:/home/ec2-user/STFC
                            """
                            // Run deployment script on EC2
                            sh """
                                ssh -o StrictHostKeyChecking=no ec2-user@${EC2_IP} << EOF
                                cd /home/ec2-user/STFC/STFC-TestServer
                                ./stfc
                                EOF
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
