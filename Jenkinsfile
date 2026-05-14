pipeline {
    agent any

    environment {
        BRANCH = "${env.BRANCH_NAME}"
        NAMESPACE = "pr-${env.BRANCH_NAME}"
        IMAGE = "simple-web:${env.BUILD_NUMBER}"
        // Manifest_file_path = "deployment.yaml"
    }

    stages {

        stage('Clone Repo') {
            steps {
                echo "Branch: ${env.BRANCH}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE} ." 
            }
        }

        stage("push to docker hub") {
            steps {
                withCredentials([usernamePassword(
                credentialsId:"${CredID}",
               passwordVariable: "dockerHubPass",
               usernameVariable: "dockerHubUser"
               )]){
               sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                //  sh "docker image tag ${ImageID} ${env.dockerHubUser}/${ImageID}"
               sh "docker image tag ${ImageID}:${env.BUILD_NUMBER} ${env.dockerHubUser}/${ImageID}:${env.BUILD_NUMBER}"
               sh "docker push ${env.dockerHubUser}/${ImageID}:${env.BUILD_NUMBER}"
               }    
            }
        }
        
        stage('Create Namespace') {
            steps {
                sh "kubectl create namespace ${env.NAMESPACE} || true"
            }
        }

        stage('Deployment Edit') {
            steps {
                sh "sed -i 's|REPLACE|${env.BUILD_NUMBER}|g' deployment.yaml"
            }
        }

        stage('Deploy') {
            steps {
                sh """
                kubectl apply -f deployment.yaml -n ${env.NAMESPACE}
                kubectl apply -f service.yaml -n ${env.NAMESPACE}
                """
            }
        }
    }
}
