pipeline {
  agent any

  environment {
    // Add Docker tool to $PATH
    tools {
      'org.jenkinsci.plugins.docker.commons.tools.DockerTool' 'docker'
    }
  }

  stages {
    stage('Clone repository') {
      steps {
        // Clone the repository
        checkout scm
      }
    }

    stage('Build image') {
      steps {
        // Build the Docker image
        script {
          app = docker.build("darnattp/test")
        }
      }
    }

    stage('Test image') {
      steps {
        // Test the Docker image
        script {
          app.inside {
            sh 'echo "Tests passed"'
          }
        }
      }
    }

    stage('Push image') {
      steps {
        // Push the Docker image to the registry
        script {
          docker.withTool('docker') {
            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
              app.push("${env.BUILD_NUMBER}")
            }
          }
        }
      }
    }

    stage('Trigger ManifestUpdate') {
      steps {
        // Trigger the updatemanifest job with DOCKERTAG parameter
        build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
      }
    }
  }
}
