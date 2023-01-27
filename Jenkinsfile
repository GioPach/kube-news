pipeline {
    // quem executa a pipeline: any -> qualquer agente pode exec
    // pode especificar...máquina windows, linux, container, etc. (docs)
    agent any

    stages {

        stage ('Build Docker Image') {
            steps {
                script {
                    // ${env.BUILD_ID} -> incrementa id do build (um jeito)
                    dockerapp = docker.build("pach5/kube-news:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage ('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', )
                }
            }
        }

    }


}
/*
 Depois de criar o jenkinsfile -> painel -> Build Now
 Adicionar credenciais do docker hub ao jenkins para dar push na imagem
 painel -> Manage Jenkins -> Credentials -> System -> Global credentials
 Preencher:
    Username with password
    - credenciais do docker hub
    - id = dockerhub

*/
