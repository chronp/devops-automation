pipeline{
    agent any
    tools{
        maven 'maven_3_5_0'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/chronp/devops-automation']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t 10.10.10.1/devops-integration .'
                }
            }
        }
        stage('Push to registry'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'localrepo-pwd', variable: 'localrepo-pwd')]) {
                        
                    sh 'docker login 10.10.10.1:443 -u student -p ${localrepo-pwd}'
                    }
                    sh 'docker push 10.10.10.1/devops-integration'
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'minikubecreds')
                   // sh 'kubectl get nodes'
                }
            }
        }
    }
}
