pipeline {
    environment {
        IMAGEN = "alealbaladejo/django_tutorial"
        LOGIN = 'docker-hub-credentials'
        SSH_CRED = 'vps-ssh-credentials'  
        VPS_USER = 'debian'
        VPS_HOST = '54.38.183.131'
        VPS_DIR = '/home/debian/django_tutorial'  // Ruta donde está el proyecto en el VPS
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
                    newApp = docker.build "${IMAGEN}:latest"
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
                sshagent(credentials: [SSH_CRED]) {
                sh 'ssh -o StrictHostKeyChecking=no ${VPS_USER}@${VPS_HOST} "cd ${VPS_DIR} && git pull && docker compose down && docker pull ${IMAGEN}:latest && docker compose up -d && docker image prune -f"'       
                    
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







