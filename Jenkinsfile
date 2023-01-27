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
                    docker.withRegistry('https://registry.hub.docker.com', dockerCredential) {
                        dockerapp.push("${env.BUILD_ID}")
                        dockerapp.push('latest')
                    }
                }
            }
        }

        stage ('Deploy Kubernetes') {
            environment {
                tag_version = "${env.BUILD_ID}"
            }
            steps {
                withKubeConfig([credentialsId: kubeConfigCredential]) {
                    sh 'sed -i "s/{{TAG}}/$tag_version/g" ./k8s/deployment.yaml ' // atualizar versao no manifesto
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                }
            }
        }

    }


}

// Observações:
/*
 Depois de criar o jenkinsfile -> Jenkins -> Build Now
 Adicionar credenciais do docker hub ao jenkins para dar push na imagem
 Jenkins -> Manage Jenkins -> Credentials -> System -> Global credentials

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
// Jenkins -> Manage Jenkins -> In-process Script Approval -> aprovar método pendente

// ? dockerapp.pushes devem estar dentro do escopo de docker.withRegistry (logar antes)
// ? pode-se usar variáveis de ambiente com environment {} e refs pelo nome

// Automatizar Build Now:
// Jenkins -> Projeto -> Configure -> Habilitar GitHub hook trigger
// GitHub -> Projeto -> Settings -> Webhooks -> Add WebHook:
// payload url = endereço jenkins + nome do webhook + "/":
//    - http://134.209.44.10:8080/github-webhook/