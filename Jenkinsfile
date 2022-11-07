properties([pipelineTriggers([githubPush()])])
pipeline {
    agent any
    environment {
        dockerhub_creds = credentials('dockerhub_creds')
    }
    tools {
        maven 'MAVEN'
    }
    stages {
        stage("checkout SCM") {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/musatee/deploy_eks_Bkash_task3.git']]])
            }
        }
        stage("Code Build") {
            steps {
                sh "mvn clean install"
            }
        }
        stage("Docker Build and Push") {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub_creds') {

                    def customImage = docker.build("musatee11/springboot:${env.BUILD_ID}")
                    customImage.push()
                    }
                }
            }
        
        } 
        stage("Remove built image from Local") {
            steps {
                sh "docker rmi -f musatee11/springboot:${env.BUILD_ID}"
            }
        }
        stage("Render image_tag to k8s manifest") {
            steps {
                sh """
                sed -i 's#image: musatee11/springboot.*#image: musatee11/springboot:${env.BUILD_ID}#g' k8s_manifest.yml
                
                """
            }
        }
        stage("Deploy to EKS") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kube_config', namespace: '', serverUrl: '') {
                        sh 'kubectl apply -f k8s_manifest.yml'
                    }
               
            }
        }
       
}
 post {
        always {
            cleanWs()
            }
        success {
            slackSend channel: 'jenkins_cicd', message: "Deployment to EKS is successful with build ID: ${env.BUILD_ID}!!"
        } 
        }
    }