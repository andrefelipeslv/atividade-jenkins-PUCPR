pipeline {
    agent any

    environment {
        MYSQL_ROOT_PASSWORD = "root123"
        MYSQL_DATABASE = "docker_e_kubernetes"
        MYSQL_USER = "user"
        MYSQL_PASSWORD = "pass123"
        MYSQL_CONTAINER_NAME = "atividade02-mysql"
        FLASK_CONTAINER_NAME = "atividade02-flask"
    }

    stages {
        stage('Cleanup') {
            steps {
                script {
                    echo "Removendo containers e imagens antigas, se existirem..."
                    sh "docker rm -f ${env.FLASK_CONTAINER_NAME} || true"
                    sh "docker rm -f ${env.MYSQL_CONTAINER_NAME} || true"
                    sh "docker rmi atividade02-flask || true"
                    sh "docker rmi atividade02-mysql || true"
                }
            }
        }

        stage('Checkout') {
            steps {
                echo "Código já está disponível via Jenkins SCM"
            }
        }

        stage('Construção') {
            steps {
                script {
                    echo "Construindo imagens Docker..."
                    // Build da imagem MySQL
                    sh "docker build -f ./atividade02/db/Dockerfile.mysql -t atividade02-mysql ./atividade02/db"
                    // Build da imagem Flask
                    sh "docker build -f ./atividade02/web/Dockerfile.web -t atividade02-flask ./atividade02/web"
                }
            }
        }

        stage('Entrega') {
            steps {
                script {
                    echo "Rodando containers..."

                    // Rodar container MySQL
                    sh """
                    docker run -d \
                        --name ${env.MYSQL_CONTAINER_NAME} \
                        -e MYSQL_ROOT_PASSWORD=${env.MYSQL_ROOT_PASSWORD} \
                        -e MYSQL_DATABASE=${env.MYSQL_DATABASE} \
                        -e MYSQL_USER=${env.MYSQL_USER} \
                        -e MYSQL_PASSWORD=${env.MYSQL_PASSWORD} \
                        atividade02-mysql
                    """

                    // Rodar container Flask com link para MySQL
                    sh """
                    docker run -d \
                        --name ${env.FLASK_CONTAINER_NAME} \
                        -p 8200:8200 \
                        -e MYSQL_USERNAME=${env.MYSQL_USER} \
                        -e MYSQL_PASSWORD=${env.MYSQL_PASSWORD} \
                        -e MYSQL_ADDRESS=${env.MYSQL_CONTAINER_NAME} \
                        -e MYSQL_DBNAME=${env.MYSQL_DATABASE} \
                        --link ${env.MYSQL_CONTAINER_NAME}:mysql \
                        atividade02-flask
                    """
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finalizada!"
        }
    }
}

