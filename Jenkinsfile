pipeline {
    agent {label "dev"};

    stages {

        stage('Clone Code') {
            steps {
                git url: 'https://github.com/suparegaurav004-sketch/Gaurav-flask-app.git', branch: 'master'
            }
        }

        stage('Clean Old Containers') {
            steps {
                sh '''
                echo "Removing old flask container if exists..."
                docker rm -f flask-app || true
                docker rm -f mysql || true
                '''
            }
        }

        stage("Build docker image"){
            steps {
                sh "docker build -t two-tear-flaskapp:latest ."
            }
        }

        stage("pushing image to docker hub"){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubcred",
                    usernameVariable: "dockerHubUser",
                    passwordVariable: "dockerHubPass"
                )]){
                    sh "docker login -u ${dockerHubUser} -p ${dockerHubPass}"
                    sh "docker image tag two-tear-flaskapp:latest ${dockerHubUser}/two-tear-flaskapp:latest"
                    sh "docker push ${dockerHubUser}/two-tear-flaskapp:latest"
                }
            }
        }

        stage('Deploy Using Docker Compose') {
            steps {
                sh '''
                echo "Stopping old compose stack..."
                docker compose down || true

                echo "Building and starting new containers..."
                docker compose up -d --build
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                echo "Checking running containers..."
                docker ps
                '''
            }
        }

    }

    post {
        success {
            script{
                emailext from:'abhimore2305@gmail.com',
                    to:'abhimore2305@gmail.com',
                    body:'Build sucess for demo CICD APP',
                    subject:'Build sucess for demo CICD APP'
            }
                
        }

        failure {
            script{
             emailext from:'abhimore2305@gmail.com',
                    to:'abhimore2305@gmail.com',
                    body:'Build fail for demo CICD APP',
                    subject:'Build fail for demo CICD APP'
            }
        }
    }
}
