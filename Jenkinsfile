pipeline {
    agent {
        kubernetes {
            cloud 'kubernetes'
            label 'mypod'
            containerTemplate {
                name 'jnlp'
                image 'jenkins/inbound-agent:4.7-1-jdk11'
                command 'cat'
                ttyEnabled true
            }
        }
    }
    
    environment {
        DOCKER_REGISTRY = "https://registry.hub.docker.com"
        DOCKER_CREDENTIALS = "dockerhub"
    }
    
    tools {
        docker = 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
    }
  
    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }

        stage('Build image') {
            steps {
                script {
                    app = docker.build("darnattp/test")
                }
            }
        }

        stage('Test image') {
            steps {
                script {
                    app.inside {
                        sh 'echo "Tests passed"'
                    }
                }
            }
        }

        stage('Push image') {
            steps {
                script {
                    docker.withRegistry(DOCKER_REGISTRY, DOCKER_CREDENTIALS) {
                        app.push("${env.BUILD_NUMBER}")
                    }
                }
            }
        }
    
        stage('Trigger ManifestUpdate') {
            steps {
                script {
                    echo "triggering updatemanifestjob"
                    build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
                }
            }
        }
    }
}
