pipeline {
    environment {
        registry = "dogbern/capstone-project-green-app"
        registryCredential = 'dockerhub_id'
        dockerImage = ''
    }
    agent any
    
    stages {
        stage('Cloning Git') {
            steps {
                git 'https://github.com/dogbern/udacity-cloud-devops-capstone.git'
            }
        }
        stage('Lint') {
            steps {
                sh 'hadolint --ignore DL3013 $WORKSPACE/Dockerfile'
                sh 'tidy -q -e $WORKSPACE/templates/index.html'
            }
        }
        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    withDockerRegistry(registry: [credentialsId: registryCredential]) {
                        dockerImage.push()
                    }       
                }
            }
        }
        stage('Security Scan Image') {
            steps {
                aquaMicroscanner imageName: "dogbern/capstone-project-green-app:$BUILD_NUMBER", notCompliesCmd: 'exit 1', onDisallowed: 'fail', outputFormat: 'html'
            }
        }
        stage('Remove Image from Jenkins') {
            steps {
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
      
    }
}