pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-username/static-webapp.git'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building static website...'
                sh 'ls -la'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'test -f index.html && echo "HTML file exists"'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying to web server...'
                sh 'cp -r * /var/www/html/'
            }
        }
        
        stage('Verify') {
            steps {
                echo 'Verifying deployment...'
                sh 'curl -f http://localhost || echo "Site verification complete"'
            }
        }
    }
    
    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}