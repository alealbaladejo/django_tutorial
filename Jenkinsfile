pipeline {
    agent {
        docker { image 'python:3'
        args '-u root:root'
        }
    }
    stages {
        stage('Clonar repo') {
            steps {
                git branch:'master',url:'https://github.com/alealbaladejo/django_tutorial.git'
            }
        }
        stage('Instalar requeriments') {
            steps {
		sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Test')
        {
            steps {
                sh 'python3 manage.py test'
            }
        }
    }
}
