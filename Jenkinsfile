pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "human537/cicdtest"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {       
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }            
            echo 'Running Build Docker Image'
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {         
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }                  
            echo 'Running Push Docker Image'                
            }
        }                
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {                
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'
                    sh 'chmod u+x ./kubectl'
                    sh './kubectl apply -f nginx-kube.yaml -n default'
                }                
            }
        }
    }
}
