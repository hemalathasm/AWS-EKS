pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin/:$PATH"
        registry = "879381269658.dkr.ecr.us-east-2.amazonaws.com/springboot"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/hemalathasm/AWS-EKS.git']])
            }
        }
        stage("Build Jar") {
            steps {
                sh "mvn clean install"
            }
        }
        stage("Build image") {
            steps {
                script {
                    dockerImage = docker.build registry
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
        stage("Push Image") {
            steps {
                script {
                    sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 879381269658.dkr.ecr.us-east-2.amazonaws.com'
                    sh 'docker push 879381269658.dkr.ecr.us-east-2.amazonaws.com/springboot:$BUILD_NUMBER'
                }
            }
        }
        stage("helm deploy") {
            steps {
                script {
                    sh "helm upgrade first --install demo-eks --namespace helm-dep --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}
