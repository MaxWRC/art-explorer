pipeline {
    agent any

    environment {
        IMAGE_NAME = "yaunpetesurleyz/art-explorer"
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker build -t test-art-explorer -f test.Dockerfile .'
                sh 'docker run --rm test-art-explorer'
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker pull $IMAGE_NAME
                    docker stop art-explorer || true
                    docker rm art-explorer || true
                    docker run -d -p 5000:5000 --name art-explorer $IMAGE_NAME
                '''
            }
        }
    }
}
