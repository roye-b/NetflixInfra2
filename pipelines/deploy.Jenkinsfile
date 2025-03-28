pipeline {
    agent {
      label 'general'
    }

    parameters {
        string(name: 'SERVICE_NAME', defaultValue: '', description: '')
        string(name: 'IMAGE_FULL_NAME_PARAM', defaultValue: '', description: '')
    }

    stages {
        stage('Setup') {
            steps {
                sh 'git checkout -b main || git checkout main'
            }
        }
        stage('update YAML manifest') {
            steps {
                sh '''
                  cd k8s/$SERVICE_NAME
                  yq e -i ".spec.template.spec.containers[0].image = \\"$IMAGE_FULL_NAME_PARAM\\"" deployment.yaml

                  git add deployment.yaml
                  git commit -m "Version updated for $SERVICE_NAME - $IMAGE_FULL_NAME_PARAM"
                '''

            }
        }
        stage('Git push') {
            steps {
               withCredentials([
                usernamePassword(credentialsId: 'github', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')
               ]) {

                 sh 'git push https://$GITHUB_USERNAME:$GITHUB_TOKEN@github.com/alonitac/NetflixInfra2.git main'

               }
            }
        }

    }

    post {
        cleanup {
            cleanWs()
        }
    }
}