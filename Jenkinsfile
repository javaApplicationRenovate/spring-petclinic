// TODO: Remove comment block after successful jenkins testing
// pipeline {
//     agent any
//     environment {
//         CI = 'true'
//     }
//     stages {
//         stage('Build') {
//             steps {
//                 println "Build WORKSPACE ${WORKSPACE}"
//                 sh '''
//                 ls -al && pwd
//                 ./mvnw clean install
//                 docker build -t pet-clinic:latest .
//                 docker images
//                 '''
//             }
//         }
//     }
// }

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
                    sh "docker images"
                    }
                }
            }
        }
        stage('Generate Application SBOM') {
            steps{
              script{
                    env.ENTERPRISE_CONTAINER_BUILD_REPO = "localhost:5000"
                    env.CONCERT_URL = "https://annie-concert1.fyre.ibm.com:12443"
                    env.CONCERT_USERNAME = "ibmconcert"
                    env.CONCERT_PASSWORD = "password"
                    env.CONCERTCTL_CMDB_URL = "http://127.0.0.1:8000/api"

                    println("testing auto build - disabled")
                    println "Build WORKSPACE ${WORKSPACE}"
                    println "JOB_NAME ${JOB_NAME}"
                    println "GIT_URL: ${GIT_URL}"
                    println "WORKSPACE: ${WORKSPACE}"
                    println "BUILD_NUMBER: ${BUILD_NUMBER}"
                    println "CODE_SOURCE_DIR_IN_WORKSPACE: ${CODE_SOURCE_DIR_IN_WORKSPACE}"
                    println "ENTERPRISE_CONTAINER_BUILD_REPO: ${ENTERPRISE_CONTAINER_BUILD_REPO}"
                    println "CI_ENV_TARGET: ${CI_ENV_TARGET}"
                    println "CONCERT_URL: ${CONCERT_URL}"
                    println "CONCERT_INSTANCE_ID: ${CONCERT_INSTANCE_ID}"
                    println "CONCERTCTL_CMDB_URL: ${CONCERTCTL_CMDB_URL}"
                    println "CONCERT_USERNAME: ${CONCERT_USERNAME}"
                    println "CONCERT_PASSWORD: ${CONCERT_PASSWORD}"
                    println "CONTAINER_BIN: ${CONTAINER_BIN}"
                    println "GIT_BIN: ${GIT_BIN}"
                    println "DIR_FILES: ${DIR_FILES}"
                    sh "/var/lib/jenkins/lib/concert_ctl_python --app --env"
              }
          }
        }
    }
}
