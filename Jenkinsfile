pipeline {
    agent any
    
    stages {
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
                sh 'mkdir -p /tmp/webapp && cp -r * /tmp/webapp/'
                echo 'Files deployed to /tmp/webapp/'
            }
        }
        
        stage('Verify') {
            steps {
                echo 'Verifying deployment...'
                sh 'ls -la /tmp/webapp/ && echo "Deployment verified"'
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