pipeline {
    // quem executa a pipeline: any -> qualquer agente pode exec
    // pode especificar...máquina windows, linux, container, etc. (docs)
    agent any

    stages {

        stage ('Build Docker Image') {
            steps {
                script {
                    // ${env.BUILD_ID} -> incrementa id do build (um jeito)
                    dockerapp = docker.build('pach5/kube-news:${env.BUILD_ID}', '-f ./src/Dockerfile ./src')
                }
            }
        }

    }


}