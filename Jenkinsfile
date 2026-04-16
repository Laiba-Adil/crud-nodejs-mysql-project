pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Laiba-Adil/crud-nodejs-mysql-project.git', 
                    branch: 'main',
                    clean: true
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    cd $WORKSPACE
                    docker-compose down || true
                    docker-compose up -d
                    sleep 20
                '''
            }
        }

        stage('Database Setup') {
            steps {
                sh '''
                    # Create the customer table if it doesn't exist
                    docker exec mysql_db mysql -uroot -proot -e "USE testdb; CREATE TABLE IF NOT EXISTS customer (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(100) NOT NULL, email VARCHAR(100) NOT NULL UNIQUE, phone VARCHAR(20), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP);" || true
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    curl -f http://localhost:3001 || exit 1
                '''
            }
        }
    }

    post {
        always {
            sh 'docker-compose logs --tail=50 || true'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed! Check logs above.'
        }
    }
}

