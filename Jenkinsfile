pipeline {
  agent any

  stages {
    stage("Build Docker Image") {
        steps {
            script {
                // Construir a imagem Docker
                docker.build("lucasgmarques/kube-news:${env.BUILD_ID}", "-f ./src/Dockerfile ./src")
              
            }
        }
    }

    stage("Push Docker Image") {
        steps {
            script {
                // Enviar a imagem Docker para o registro
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                  docker.image("lucasgmarques/kube-news:${env.BUILD_ID}").push()
                }
            }
        }
    }

    stage("Deploy no k8s") {
        environment {
            tag_version = "${env.BUILD_ID}"
        }
        steps {
            withKubeConfig([credentialsId: 'kubeconfig']){
                sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/deployment.yaml'
            }
        }
    }
  }
}
