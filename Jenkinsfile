pipeline {
    agent any

    environment {
        EC2_USER = "ubuntu"                  // Change to your EC2 user (e.g., ec2-user for Amazon Linux)
        EC2_HOST = "43.204.25.64"      // Replace with your EC2 instance IP
        APP_DIR = "/home/ubuntu/first-project/app" // Path to app directory on EC2
        SSH_KEY = "/var/lib/jenkins/server.pem"    // Path to your EC2 private key
        APP_PORT = "5000"                    // Flask application port
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to EC2') {
    steps {
        script {
            echo "Copying updated application files to EC2..."
            sh '''
            scp -o StrictHostKeyChecking=no -i /var/lib/jenkins/server.pem -r app/main.py app/templates ubuntu@43.204.25.64:/home/ubuntu/first-project/app/
            '''
            
            echo "Restarting application on EC2..."
            sh '''
            ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/server.pem ubuntu@43.204.25.64<<EOF
            sudo pkill -f gunicorn || echo "Gunicorn process not found"
            cd /home/ubuntu/first-project/app
            source /home/ubuntu/first-project/venv/bin/activate
            nohup gunicorn -w 4 -b 0.0.0.0:5000 main:app > gunicorn.log 2>&1 &
            exit
EOF
            '''
        }
    }
}


}

    post {
        success {
            echo 'Deployment to EC2 successful!'
        }
        failure {
            echo 'Deployment failed. Check the logs for details.'
        }
    }
}
