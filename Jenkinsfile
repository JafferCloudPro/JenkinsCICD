pipeline {
    agent any

    environment {
        PROJECT_ID = 'diesel-talon-436013-q7'
        CLUSTER_NAME = 'my-cluster'
        LOCATION = 'us-central1-a'
        CREDENTIALS_ID = '5c0ef4e8-9e97-469c-a541-6ef5f650dd40'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/JafferCloudPro/JenkinsCICD.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t gcr.io/$PROJECT_ID/petclinic:latest .'
            }
        }

        stage('Push to Container Registry') {
            steps {
                withCredentials([string(credentialsId: CREDENTIALS_ID, variable: 'TOKEN')]) {
                    sh 'docker login -u _json_key --password-stdin https://gcr.io < $TOKEN'
                    sh 'docker push gcr.io/$PROJECT_ID/petclinic:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                kubernetesDeploy(
                    configs: 'k8s/deployment.yaml',
                    kubeconfigId: CREDENTIALS_ID,
                    enableConfigSubstitution: true,
                    namespace: 'default'
                )
            }
        }
    }
}
