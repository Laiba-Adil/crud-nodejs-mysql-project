pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Laiba-Adil/crud-nodejs-mysql-project.git', 
                    branch: 'main'
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f mysql_db node_app || true
                    docker-compose -f $WORKSPACE/docker-compose.yml down --remove-orphans || true
                    docker-compose -f $WORKSPACE/docker-compose.yml up -d
                    sleep 40
                    docker ps
                '''
            }
        }
        stage('Database Setup') {
            steps {
                sh '''
                    docker exec mysql_db mysql -uroot -proot -e "USE testdb; CREATE TABLE IF NOT EXISTS customer (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(100) NOT NULL, email VARCHAR(100) NOT NULL UNIQUE, phone VARCHAR(20), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP);" || true
                '''
            }
        }
        stage('Health Check') {
            steps {
                sh '''
                    sleep 10
                    curl -f http://localhost:3001 || exit 1
                '''
            }
        }
    }
    post {
        always {
            sh 'docker-compose -f $WORKSPACE/docker-compose.yml logs --tail=50 || true'
            // REMOVED cleanWs() - it was killing containers
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed! Check logs above.'
        }
    }
}
