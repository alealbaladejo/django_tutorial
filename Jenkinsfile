pipeline {
    agent none  // No se ejecuta en un solo agente, usaremos dos diferentes

    stages {
        stage('Test en Docker') {
            agent {
                docker { 
                    image 'python:3' 
                    args '-u root:root' 
                }
            }
            steps {
                stage('Clone') {
                    steps {
                        git branch: 'master', url: 'https://github.com/alealbaladejo/django_tutorial'
                    }
                }
                stage('Install') {
                    steps {
                        sh 'pip install -r requirements.txt'
                    }
                }
                stage('Test') {
                    steps {
                        sh 'python3 manage.py test'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            agent { label 'jenkins-node' }  // Se ejecuta en el nodo de Jenkins
            steps {
                sh '''
                    docker build -t alealbaladejo/django_tutorial:latest .
                '''
            }
        }

        stage('Push to Docker Hub') {
            agent { label 'jenkins-node' }
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker push alealbaladejo/django_tutorial:latest'
                }
            }
        }

        stage('Clean up') {
            agent { label 'jenkins-node' }
            steps {
                sh 'docker rmi alealbaladejo/django_tutorial:latest'
            }
        }
    }

    post {
        success {
            mail to: 'alealbaladejo29s@gmail.com',
                subject: 'Jenkins Pipeline Éxito',
                body: 'El pipeline ha finalizado correctamente y la imagen ha sido subida a Docker Hub.'
        }
        failure {
            mail to: 'alealbaladejo29s@gmail.com',
                subject: 'Jenkins Pipeline Fallido',
                body: 'El pipeline ha fallado. Revisa los logs de Jenkins.'
        }
    }
}
