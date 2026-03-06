pipeline {
    agent any

    environment {
        GITHUB_CREDS = credentials('github-creds')
        DOCKERHUB_CREDS = credentials('dockerhub-creds')
        DOCKER_IMAGE = "your-dockerhub-username/ci-cd-java-docker"
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/vimalmahalingam/ci-cd-java-docker.git',
                    credentialsId: 'github-creds'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'javac src/App.java src/AppTest.java'
                sh 'java -cp src AppTest'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t $DOCKER_IMAGE:${env.BUILD_NUMBER} ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh "echo $DOCKERHUB_CREDS_PSW | docker login -u $DOCKERHUB_CREDS_USR --password-stdin"
                sh "docker push $DOCKER_IMAGE:${env.BUILD_NUMBER}"
            }
        }

        stage('Deploy') {
            steps {
                sh "docker run -d -p 8080:8080 $DOCKER_IMAGE:${env.BUILD_NUMBER}"
            }
        }
    }
}
