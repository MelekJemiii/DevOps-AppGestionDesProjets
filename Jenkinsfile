pipeline {
    agent any

    environment {
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build images') {
            steps {
                sh 'docker compose build'
            }
        }

        stage('Push images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                                  usernameVariable: 'DH_USER',
                                                  passwordVariable: 'DH_PASS')]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        for s in backend frontend; do
                          docker tag gestion-$s $DH_USER/gestion-$s:$TAG
                          docker tag gestion-$s $DH_USER/gestion-$s:latest
                          docker push $DH_USER/gestion-$s:$TAG
                          docker push $DH_USER/gestion-$s:latest
                        done
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose up -d'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
