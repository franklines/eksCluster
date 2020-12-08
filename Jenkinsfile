pipeline {
    agent any
    parameters {
        string(name: 'AWS_ACCESS_ID', defaultValue: 'AWS_ACCESS_ID', description: 'AWS_ACCESS_ID')
        password(name: 'AWS_SECRET_KEY', defaultValue: 'AWS_SECRET_KEY', description: 'AWS_SECRET_KEY')
    }
    stages {
        stage('Build tools container') {
            steps {
                sh('docker build -t tools:latest -f docker/Dockerfile .')
            }
        }
        stage('Create Cluster') {
            environment {
                def CMD = 'cd /etc/eks/ && eksctl create cluster -f cluster.yml'
            }
            steps {
                sh '''
                    set +x
                    docker run --name tools -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_KEY tools:latest bash -c "$CMD"
                '''
            }
        }
    }
    post {
        always {
            sh('docker rm tools')
        }
    }
}
