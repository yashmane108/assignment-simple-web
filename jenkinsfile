pipeline {
    agent any

    environment {
        BRANCH = "${env.BRANCH_NAME}"
        NAMESPACE = "pr-${env.BRANCH_NAME}"
        IMAGE = "simple-web:${env.BUILD_NUMBER}"
        Manifest_file_path = "eks/deployment.yaml"
    }

    stages {

        stage('Clone Repo') {
            steps {
                echo "Branch: ${BRANCH}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE} ." 
            }
        }

        stage('Docker: Image Push') {
            steps {
                dockerHubPushCred("dockerCred", "simple-app")
            }
        }

        stage('Create Namespace') {
            steps {
                sh """
                kubectl create namespace ${NAMESPACE} || true
            }
        }

        stage('Deployment Edit') {
            steps {
                sh "sed -i 's|REPLACE|${env.BUILD_NUMBER}|g' ${env.Manifest_file_path}"
            }
        }

        stage('Deploy') {
            steps {
                sh """
                kubectl apply -f ${env.Manifest_file_path} -n ${NAMESPACE}
                kubectl apply -f ${env.Manifest_file_path} -n ${NAMESPACE}
                """
            }
        }
    }
}
