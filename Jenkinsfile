pipeline {
    agent any

    environment {
        DOCKER_BFLASK_IMAGE = 'nethmipoornima/my-flask-app:latest'
        DOCKER_REGISTRY_CREDS = 'docker-jenkins-token1'
        DOCKER_CMD = '/usr/local/bin/docker'  // full path to Docker
        PATH = "/usr/local/bin:${env.PATH}"   // ensure Jenkins can find Docker
    }

    stages {
        stage('Build') {
            steps {
                sh "${DOCKER_CMD} build -t my-flask-app ."
                sh "${DOCKER_CMD} tag my-flask-app ${DOCKER_BFLASK_IMAGE}"
            }
        }

        stage('Test') {
            steps {
                sh "${DOCKER_CMD} run --rm my-flask-app python3 -m pytest app/tests/"
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_REGISTRY_CREDS}",
                        passwordVariable: 'DOCKER_PASSWORD',
                        usernameVariable: 'DOCKER_USERNAME'
                    )
                ]) {
                    sh "echo $DOCKER_PASSWORD | ${DOCKER_CMD} login -u $DOCKER_USERNAME --password-stdin docker.io"
                    sh "${DOCKER_CMD} push ${DOCKER_BFLASK_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            sh "${DOCKER_CMD} logout || true"
        }
    }
}
