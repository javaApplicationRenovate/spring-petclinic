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
                ./mvnw clean install
                docker build -t pet-clinic:latest .
                docker images
                '''
            }
        }
    }
}
