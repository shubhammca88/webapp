pipeline {
    agent { label 'ec2-agent' }
    
    environment {
        SERVER_HOST = sh(script: 'hostname -I | awk "{print \$1}"', returnStdout: true).trim()
        SERVER_USER = sh(script: 'whoami', returnStdout: true).trim()
        NGINX_ROOT = '/var/www/html'
        APP_NAME = 'webapp'
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building static website......'
                sh 'ls -la'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'test -f index.html && echo "HTML file exists"'
            }
        }
        
        stage('Configure EC2 Server') {
            steps {
                echo 'Configuring EC2 server...'
                sh '''
                    # Install required packages
                    sudo apt update
                    sudo apt install -y ufw
                    
                    # Configure firewall
                    sudo ufw allow 22/tcp
                    sudo ufw allow 8080/tcp
                    sudo ufw --force enable
                    
                    echo "Firewall configured for port 8080"
                '''
            }
        }
        
        stage('Install Nginx') {
            steps {
                echo 'Installing nginx on current agent...'
                sh '''
                    sudo apt install -y nginx
                    sudo systemctl enable nginx
                    sudo systemctl start nginx
                '''
            }
        }
        
        stage('Deploy to Server') {
            steps {
                echo 'Deploying to nginx on current agent...'
                sh '''
                    sudo mkdir -p ${NGINX_ROOT}/${APP_NAME}
                    sudo cp -r *.html *.css *.js images/ ${NGINX_ROOT}/${APP_NAME}/ 2>/dev/null || sudo cp -r * ${NGINX_ROOT}/${APP_NAME}/
                    sudo rm -f ${NGINX_ROOT}/${APP_NAME}/Jenkinsfile
                    sudo chown -R www-data:www-data ${NGINX_ROOT}/${APP_NAME}
                    sudo chmod -R 755 ${NGINX_ROOT}/${APP_NAME}
                    echo "Files deployed:"
                    sudo ls -la ${NGINX_ROOT}/${APP_NAME}/
                '''
            }
        }
        
        stage('Configure Nginx') {
            steps {
                echo 'Configuring nginx...'
                sh '''
                    # Remove default nginx site
                    sudo rm -f /etc/nginx/sites-enabled/default
                    
                    # Create webapp config
                    sudo tee /etc/nginx/sites-available/${APP_NAME} > /dev/null <<EOF
server {
    listen 8080;
    server_name _;
    root ${NGINX_ROOT}/${APP_NAME};
    index index.html;
    
    location / {
        try_files \\\$uri \\\$uri/ /index.html;
    }
}
EOF
                    sudo ln -sf /etc/nginx/sites-available/${APP_NAME} /etc/nginx/sites-enabled/
                    sudo nginx -t
                    sudo systemctl reload nginx
                    
                '''
            }
        }
        
        stage('Verify') {
            steps {
                echo 'Verifying deployment...'
                sh '''
                    sudo systemctl status nginx --no-pager
                    echo "Testing webapp access:"
                    curl -I http://localhost:8080/ || echo "Service check failed"
                    echo "Checking if index.html exists:"
                    sudo test -f ${NGINX_ROOT}/${APP_NAME}/index.html && echo "index.html found" || echo "index.html missing"
                '''
            }
        }
    }
    
    post {
        success {
            echo "✅ Deployment successful! App available at http://3.110.210.57:8080/"
        }
        failure {
            echo '❌ Deployment failed!'
        }
        cleanup {
            echo 'Cleanup completed'
        }
    }
}