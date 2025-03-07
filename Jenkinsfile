pipeline {
    agent {
        docker { image 'python:latest'
        args '-u root:root'
        }
    }
    stages {
        stage('Clone') {
            steps {
                git branch:'master',url:'https://github.com/alealbaladejo/django_tutorial.git'
            }
        }
        stage('Install') {
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
