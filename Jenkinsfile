node {
    def app

    stage('Clone repository') {
        /*  Clone repository from github */
        checkout scm
    }

    stage('Build image') {
        /* This pulls and builds the actual image */
        app = docker.build("lewiedun/node-web-app")
    }

    stage('Test image inside container') {
        /* Run a echo command inside container to make sure its
        * launched correctly */

        app.inside {
            sh 'echo "Test successful"'
        }
    }

    stage('Push image') {
        /* Pushing the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         */
        docker.withRegistry('https://registry.hub.docker.com', 'DockerhubId') {
            app.push("${env.BUILD_NUMBER}")
            app.push("Updated")
        }
    }    
    
    stage('Connect to production envir,  pull image, and launch update') {
        /* This connects to the production server,
        pulls the image and rolls out an update */
        sshagent(['my-ssh-key']) {
            sh 'ssh ubuntu@ip-172-31-81-121 kubectl set image deployments/server-js node-web-app=lewiesimage:Updated'
        }
    }
}
