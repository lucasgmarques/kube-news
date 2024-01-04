pipeline {
  agent any

  environment {
    app_version = "1.0.0"
  }

  stages {
    stage("Build Docker Image") {
        steps {
            script {
                // Construir a imagem Docker
                docker.build("lucasgmarques/kube-news:${env.app_version}", "-f ./src/Dockerfile ./src")
              
            }
        }
    }

    stage("Push Docker Image") {
        steps {
            script {
                // Enviar a imagem Docker para o registro
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                  docker.image("lucasgmarques/kube-news:${env.app_version}").push()
                }
            }
        }
    }

    stage("Deploy to Kubernetes") {
        steps {
            withKubeConfig([credentialsId: 'kubeconfig']){
                sh 'sed -i "s/{{TAG}}/$app_version/g" ./k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }
    }
  }
}
