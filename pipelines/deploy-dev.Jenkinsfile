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
                sh 'git checkout -b dev || git checkout dev'
            }
        }
        stage('update YAML manifest') {
            steps {
                sh '''
                  cd k8s/dev/$SERVICE_NAME
                   #
                  #yq e -i ".spec.template.spec.containers[0].image = \"$IMAGE_FULL_NAME_PARAM\"" deployment.yaml

                  yamlFile=deployment.yaml
                  if [ -f "${yamlFile}" ]; then
                      sed -i "s|image: .*|image: ${IMAGE_FULL_NAME_PARAM}|" ${yamlFile}
                  else
                      echo "ERROR: ${yamlFile} not found!"
                      exit 1
                  fi

                  git add deployment.yaml
                  git commit -m "Version updated for $SERVICE_NAME - $IMAGE_FULL_NAME_PARAM"
                '''
            }
        }
        stage('Git push') {
            steps {
               // Change `credentialsId` according to the Id you've configured your GitHub token
               withCredentials([
                usernamePassword(credentialsId: 'github', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')
               ]) {

                 sh 'git push https://$GITHUB_TOKEN@github.com/alonitac/NetflixInfra2.git dev'

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