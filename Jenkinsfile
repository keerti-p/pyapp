pipeline {
    agent any

    environment {
        IMAGE_NAME = "patilkeerti/jenkinspyapp"
        ID = "$BUILD_ID"
    }

    stages {

        stage('git pull') {
            steps {
                git 'https://github.com/keerti-p/pyapp.git'
            }
        }

        stage('install and test') {
            steps {
                sh 'pip install -r requirements.txt'
                sh 'python3 -m pytest'
            }
        }

        stage('docker image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$ID .'
                sh 'docker tag $IMAGE_NAME:$ID $IMAGE_NAME:latest'
            }
        }

        stage('docker login') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker_cred',
                        usernameVariable: 'username',
                        passwordVariable: 'paswd'
                    )
                ]) {

                    sh 'echo "$paswd" | docker login -u "$username" --password-stdin'
                }
            }
        }

        stage('push image') {
            steps {
                sh 'docker push $IMAGE_NAME:$ID'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('deploy') {
            steps {
                sh 'docker compose up -d --build'
            }
        }
    }

    post {

        success {

            mail bcc: '',
                 body: """Hi Team,

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: SUCCESS
Branch: ${env.GIT_BRANCH}
Commit: ${env.GIT_COMMIT}

Logs: ${env.BUILD_URL}console

Regards,
Jenkins
""",
                 cc: '',
                 from: '',
                 replyTo: '',
                 subject: "[Jenkins] ${env.JOB_NAME} #${env.BUILD_NUMBER} - Success",
                 to: 'keertipatil012@gmail.com'
        }

        failure {

            mail bcc: '',
                 body: """Hi Team,

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: FAILED
Branch: ${env.GIT_BRANCH}
Commit: ${env.GIT_COMMIT}

Logs: ${env.BUILD_URL}console

Regards,
Jenkins
""",
                 cc: '',
                 from: '',
                 replyTo: '',
                 subject: "[Jenkins] ${env.JOB_NAME} #${env.BUILD_NUMBER} - Failed",
                 to: 'keertipatil012@gmail.com'
        }
    }
}
