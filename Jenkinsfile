pipeline {

    environment {
        dockerCredential = 'dockerhub'
        kubeConfigCredential = 'kubeconfig'
    }

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
                    // dockerhub = id da credencial adicionada ao Jenkins
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        dockerapp.push("${env.BUILD_ID}")
                        dockerapp.push('latest')
                    }
                }
            }
        }

        stage ('Deploy Kubernetes') {
            steps {
                withKubeconfig([credentialsId: kubeConfigCredential]) {
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }
            }
        }

    }


}

// Observações:
/*
 Depois de criar o jenkinsfile -> painel -> Build Now
 Adicionar credenciais do docker hub ao jenkins para dar push na imagem
 painel -> Manage Jenkins -> Credentials -> System -> Global credentials

 Criar credencial dockerhub:
    Username with password
    - credenciais do docker hub
    - id = dockerhub

 Criar credencial terraform:
    - Secretfile
    - adicionar kube_config.yaml gerado
    - id = kubeconfig
*/

// ? Solução para Jenkins CI Pipeline Scripts not permitted to use method:
// painel -> Manage Jenkins -> In-process Script Approval -> aprovar método pendente

// ? dockerapp.pushes devem estar dentro do escopo de docker.withRegistry (logar antes)
// ? pode-se usar variáveis de ambiente com environment {} e refs pelo nome