node{
def project = 'workspace-235803'
def appName = 'node_app'
def Namespace = 'default'
def imageTag = "gcr.io/${project}/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
def Creds	= "2dfd9d0d-a300-49ee-aaaf-0a3efcaa5279"

 try{
  stage('Checkout'){
      git 'https://github.com/sumeetoc/pipe_node.git'
      sh "git rev-parse --short HEAD > .git/commit-id"
      imageTag= readFile('.git/commit-id').trim()
  }

  stage('RUN Unit Tests'){
    dir ('./app/') { 
      sh "npm install"
      sh "npm test"
    }
  }

  stage('Docker Build, Push'){
    dir ('./app') { 
      sh "docker build -t ${imageTag} ."
      sh "echo y | gcloud auth configure-docker"
      sh "docker push ${imageTag}"
    }
  }

  stage('Deploy on K8s'){
     sh "helm upgrade --wait --recreate-pods --namespace={{ Namespace }} --set image.repository={{ imageTag }} --set namespace={{ Namespace }} node_app app/"
    }
  } catch (err) {
      currentBuild.result = 'FAILURE'
  }
}
