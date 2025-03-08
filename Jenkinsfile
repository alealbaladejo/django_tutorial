pipeline {
    environment {
        IMAGEN = "alealbaladejo/django_tutorial"
        LOGIN = 'docker-hub-credentials'
    }
    agent any
    stages {
        stage("Bajar_imagen") {
            agent {
                docker {
                    image "python:3"
                    args '-u root:root'
                }
            }
            stages {
                stage('Clonar_repositorio') {
                    steps {
                        git branch:'master',url:'https://github.com/alealbaladejo/django_tutorial.git'
                    }
                }
                stage('Instalar requirementes') {
                    steps {
			sh 'pip install --upgrade pip'
                        sh 'pip install -r requirements.txt'
                    }
                }
                stage('Probar Test')
                {
                    steps {
                        sh 'python manage.py test'
                    }
                }

            }
        }
        stage("Generar_imagen docker") {
            agent any
            stages {
                stage('Construir imagen') {
                    steps {
                        script {
                            newApp = docker.build "$IMAGEN:latest"
                        }
                    }
                }
                stage('Subir imagen a dockerhub') {
                    steps {
                        script {
                            docker.withRegistry( '', LOGIN ) {
                                newApp.push()
                            }
                        }
                    }
                }
                stage('Borrar_imagen') {
                    steps {
                        sh "docker rmi $IMAGEN:latest"
                    }
                }
            }
        }
    }
    post {
        always {
            mail to: 'alealbaladejo29s@gmail.com',
            subject: "Pipeline IC: ${currentBuild.fullDisplayName}",
            body: "${env.BUILD_URL} FINAL PIPELINE:  ${currentBuild.result}"
        }
    }
}
