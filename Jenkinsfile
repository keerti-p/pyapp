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
               sh '''pip install -r requirements.txt'''
               sh '''python3 -m pytest'''
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
               withCredentials([usernamePassword(credentialsId: 'docker_cred', passwordVariable: 'paswd', usernameVariable: 'username')]) {
    // some block
}
               sh  'echo "$pswd" | docker login -u $username --password-stdin'
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
    }
