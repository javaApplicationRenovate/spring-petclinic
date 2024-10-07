pipeline {
    agent any
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                println "Build WORKSPACE ${WORKSPACE}"
                sh '''
                ls -al && pwd
                mvn clean install
                podman build -t pet-clinic:latest .
                podman images
                '''
            }
        }
    }
}
