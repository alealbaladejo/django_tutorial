pipeline {
    agent {
        docker { image 'python:3'
        args '-u root:root'
        }
    }
    stages {
        stage('Clone') {
            steps {
                git branch:'master',url:'https://github.com/josedom24/django_tutorial.git'
            }
        }
        stage('Install') {
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
