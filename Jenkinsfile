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
                    sh "chmod +x mvnw"
                    sh "./mvnw clean install -DskipTests=true"
                    sh "docker buildx build --tag ${ENTERPRISE_CONTAINER_BUILD_REPO}/${COMPONENT_NAME}:${BUILD_NUMBER} ."
                    sh "docker login -u ${USERNAME} -p ${PASSWORD} ${ENTERPRISE_CONTAINER_BUILD_REPO}"
                    sh "docker push ${ENTERPRISE_CONTAINER_BUILD_REPO}/${COMPONENT_NAME}:${BUILD_NUMBER}"
                    }
                }
            }
        }
        stage('Generate Application SBOM') {
            steps{
                script{
                    sh "/var/lib/jenkins/lib/concert_ctl_python --app --env"
                }
            }
        }
    }
}
