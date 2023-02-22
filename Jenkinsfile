node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
      docker.withTool('docker'){
    docker.withRegistry('repo','dockerhub') { 
        app = docker.build("darnattp/test")
    }
}
       
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('Trigger ManifestUpdate') {
                echo "triggering updatemanifestjob"
                build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
}
