pipeline {
   agent any

   environment {

     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)
     // get curretn commit sha, command 'git rev-parse HEAD' return full sha
     // if you wanna push image to dockerhub, image name must be unique
     GITCOMMITSHA = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
     SERVICE_NAME = "client"
     // ORGANIZATION_NAME = 'istio-demonstration'
     dockerhub_credential='DockerHubId'
     registry = '839928622/client'
     dockerImage = ''

   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }


      stage('Build and Push Image') {
         steps {
             sh 'echo current git commit is ${GITCOMMITSHA}'
             sh 'docker image build  -t ${SERVICE_NAME}:latest -t ${SERVICE_NAME}:${GITCOMMITSHA} .'
             dockerImage = docker.build registry + ':${GITCOMMITSHA}'
         }
      }

  stage('push') {
         steps {
            docker.withRegistry('', dockerhub_credential)
            dockerImage.push()
         }
      }

      stage('Deploy to Cluster') {
          steps {
                  sh 'kubectl apply -f deploy.yaml'
                  sh 'kubectl set image deployments/web-app-experimental web-app=${SERVICE_NAME}:${GITCOMMITSHA}'
                }
      }
   }
}
