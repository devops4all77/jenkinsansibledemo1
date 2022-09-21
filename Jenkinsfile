pipeline{
    agent any
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('init'){
            steps{
                checkout scm
            }
        }
        
        stage('Build'){
            steps{
                sh "mvn clean package"
                withCredentials([usernamePassword(credentialsId: '496c76de-ea13-4d0f-a945-fec687b54851', passwordVariable: '', usernameVariable: '')]) {
                        sh "echo ${passwordVariable} |sudo -S docker build . -t devopsforyou94/helloapp:${DOCKER_TAG} "
               }
                sh "echo ""sudo docker build . -t devopsforyou94/helloapp:${DOCKER_TAG} "
                withCredentials([string(credentialsId: '3168735c-0100-4554-806f-3ef1e6b3e3a6', variable: 'dockerHubPwd')]) {
                    sh "docker login -u devopsforyou94 -p ${dockerHubPwd}"
                }
                
                sh "docker push devopsforyou94/helloapp:${DOCKER_TAG} "
            }
        }
        
        stage('Deploy'){
            steps{
              ansiblePlaybook credentialsId: '496c76de-ea13-4d0f-a945-fec687b54851', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
