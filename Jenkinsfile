pipeline {
    agent any

    environment {
        LOG_FILE = "/var/log/apache2/access.log"
    }

    stages {
        stage('Install Apache2') {
            steps {
                script {
                    echo "Installing Apache2..."
                    // Update system and install Apache
                    sh '''
                        sudo apt-get update -y
                        sudo apt-get install -y apache2
                        sudo systemctl enable apache2
                        sudo systemctl start apache2
                    '''
                }
            }
        }

        stage('Check Apache Logs for Errors') {
            steps {
                script {
                    echo "Checking Apache logs for 4xx and 5xx errors..."
                    // Search for HTTP status codes 4xx or 5xx
                    sh '''
                        if sudo grep -E "HTTP/1.[01]\" [45][0-9]{2}" ${LOG_FILE}; then
                            echo "Errors found in logs!"
                            exit 1
                        else
                            echo "No critical errors found in logs."
                        fi
                    '''
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    echo "Deploying application..."
                    // Place your deployment steps here
                    sh '''
                        echo "<h1>Apache is running!</h1>" | sudo tee /var/www/html/index.html
                    '''
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished. Cleaning workspace..."
            cleanWs()
        }
        failure {
            echo "Pipeline failed. Check the logs above for details."
        }
    }
}
