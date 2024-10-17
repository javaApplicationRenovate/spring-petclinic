pipeline {
    agent any
    environment {
        CI = 'true'
        CODE_SOURCE_DIR_IN_WORKSPACE="src"
        CONTAINER_BIN="docker"
        GIT_BIN="git"
        DIR_FILES="data"
    }
    stages {
        stage('Build') {
            steps{
              script{
                  withCredentials([usernamePassword(credentialsId: "${DOCKER_REPO_CREDENTIALS}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    env.COMPONENT_NAME="${JOB_NAME}".tokenize("/")[0]
                    sh "chmod +x ./mvnw"
                    sh "./mvnw clean install -DskipTests=true -Dcheckstyle.skip=true" 
                    sh "docker buildx build --tag ${ENTERPRISE_CONTAINER_BUILD_REPO}/${COMPONENT_NAME}:${BUILD_NUMBER} ."
                    sh "docker login -u ${USERNAME} -p ${PASSWORD} ${ENTERPRISE_CONTAINER_BUILD_REPO}"
                    sh "docker push ${ENTERPRISE_CONTAINER_BUILD_REPO}/${COMPONENT_NAME}:${BUILD_NUMBER}"
                    }
                }
            }
        }
        stage('Test concertCtl and set environment') {
            steps{
                script{      

                  withCredentials([usernamePassword(credentialsId: "CONCERT_CREDENTIALS", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    env.CONCERT_USERNAME="${USERNAME}"
                    env.CONCERT_PASSWORD="${PASSWORD}"    
                    sh "/var/lib/jenkins/lib/concert-ctl-python-test -e"

                    }
                }
            }
        }
        stage('Generate Application SBOM') {
            steps{
                script{
                    sh "/var/lib/jenkins/lib/concert-ctl-python-test --app"
                }
            }
        }
        stage('Generate Build SBOM') {
            steps{
                script{
                    sh "/var/lib/jenkins/lib/concert-ctl-python-test --build"
                }
            }
        }
        stage('Generate Deploy SBOM') {
            steps{
                script{
                    sh "/var/lib/jenkins/lib/concert-ctl-python-test --deploy"
                }
            }
        }
        stage('Generate Image Scan report') {
            steps{
                script{
                    sh "/var/lib/jenkins/lib/concert-ctl-python-test --image_scan"
                }
            }
        }
        stage('Generate Package SBOM') {
            steps{
                script{
                    sh "/var/lib/jenkins/lib/concert-ctl-python-test --image"
                }
            }
        }
    }
}
