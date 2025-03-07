pipeline {
    agent {
        docker { image 'python:3'
        args '-u root:root'
        }
    }
    stages {
        stage('Clonar repo') {
            steps {
                git branch:'master',url:'https://github.com/alealbaladejo/ic-html5.git'
            }
        }
        stage('Instalar requeriments') {
            steps {
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
