pipeline {
    agent any 
    triggers {
        GenericTrigger(
        token: 'anytokenn',
        )
    }
    environment {
        // Docker ......................
        IMAGE_TAG = "$BUILD_NUMBER"
        DOCKER_CREDENTIAL_ID = "dockerhub"
        USER_NAME = "devopsaes"
        // Git ..........................
        GIT_REPO = "https://github.com/aesakif/ci_cd_demo.git"
        GIT_BRANCH = "main"
        GIT_CREDENTIAL_ID = "github"

        APP_NAME = "imgname"
        APP_NAMESPACE = "namesp"
        UPDATE_IMAGE_JOB = "UPDATE_IMAGE"

        GO_ENDPOINT = "http://144.126.240.6/goapi/anwarcloud/update_frontend"
     }
 
    stages {
        stage('CLEANUP WORKSPACE'){
            steps{
                script{
                    cleanWs()
                }
            }
        }

        stage("CHECKOUT GIT REPO"){
            steps{
                git branch: "${GIT_BRANCH}", credentialsId: "${GIT_CREDENTIAL_ID}", url: "${GIT_REPO}"
            }
        }

        stage("PUSH DOCKER IMAGES TO DOCKERHUB"){
            steps{
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIAL_ID}", passwordVariable: 'PASSWORD', usernameVariable: 'USER_NAME')]) {

                    sh"docker build -t ${USER_NAME}/${APP_NAME}:${IMAGE_TAG} -t ${USER_NAME}/${APP_NAME}:latest ."   

                    sh"echo ${PASSWORD} | docker login --username ${USER_NAME} --password-stdin"

                    sh"docker push ${USER_NAME}/${APP_NAME}:${IMAGE_TAG}"
                    sh"docker push ${USER_NAME}/${APP_NAME}:latest"

                    sh"docker logout"

                }
            }
        }

        stage("TRIGGERING THE CONFIG PIPELINE"){
            steps{
                build job: "${UPDATE_IMAGE_JOB}", parameters: [string(name: 'GO_Endpoint', value: env.GO_ENDPOINT), string(name: 'APP_NAME', value: env.APP_NAME), string(name: 'APP_NAMESPACE', value: env.APP_NAMESPACE), string(name: 'USER_NAME', value: env.USER_NAME), string(name: 'IMAGE_TAG', value: env.IMAGE_TAG)]
            }
        }
        
    }

}
