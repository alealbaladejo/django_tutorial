pipeline {
    environment {
        IMAGEN = "alealbaladejo/django_tutorial"
        LOGIN = 'docker-hub-credentials'
        SSH_CRED = 'vps-ssh-credentials'  
        VPS_USER = 'debian'  
        VPS_HOST = '54.38.183.131'  
        VPS_DIR = '/home/debian/django_app'  // Ruta donde está el proyecto en el VPS
    }
    agent any

    triggers {
        githubPush()  // Ejecuta el pipeline cuando haya un push en GitHub
    }

    stages {
        stage("Test en Docker") {
            agent {
                docker {
                    image "python:3"
                    args '-u root:root'
                }
            }
            steps {
                git branch: 'master', url: 'https://github.com/alealbaladejo/django_tutorial.git'
                sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
                sh 'python manage.py test'
            }
        }

        stage("Generar imagen Docker") {
            steps {
                script {
                    sh 'ls -l'  // Verificar si el Dockerfile está presente
                    newApp = docker.build("${IMAGEN}:latest")
                }
            }
        }

        stage("Subir imagen a Docker Hub") {
            steps {
                script {
                    docker.withRegistry('', LOGIN) {
                        newApp.push()
                    }
                }
            }
        }

        stage("Borrar imagen local") {
            steps {
                sh "docker rmi ${IMAGEN}:latest"
            }
        }

        stage("Desplegar en VPS") {
            steps {
                script {
                    sshagent(credentials: [SSH_CRED]) {
                        sh """
                        echo "🔄 Sincronizando archivos con el VPS..."
                        rsync -avz --delete ./docker-compose.yaml .env ${VPS_USER}@${VPS_HOST}:${VPS_DIR}/

                        ssh -o StrictHostKeyChecking=no ${VPS_USER}@${VPS_HOST} << 'EOF'
                        echo "📌 Navegando al directorio de la aplicación..."
                        cd ${VPS_DIR}

                        echo "📌 Bajando el contenedor antiguo..."
                        docker compose stop app
                        docker compose rm -f app

                        echo "📌 Descargando la nueva imagen..."
                        docker pull ${IMAGEN}:latest

                        echo "📌 Levantando el nuevo contenedor..."
                        docker compose up -d app

                        echo "⌛ Esperando 10 segundos para verificar estado..."
                        sleep 10

                        echo "📌 Mostrando logs recientes..."
                        docker compose logs --tail=20 app

                        echo "🧹 Limpiando imágenes antiguas..."
                        docker image prune -f
                        exit
                        EOF
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            emailext(
                to: 'alealbaladejo29s@gmail.com',
                subject: "Pipeline IC: ${currentBuild.fullDisplayName}",
                body: "${env.BUILD_URL} ha terminado con estado ${currentBuild.result}"
            )
        }
    }
}
