pipeline{
    agent any
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('init'){
            steps{
                git credentialsId: '55efbada-74ac-44e5-99af-b7b304b4baee', 
                    url: 'https://github.com/devops4all77/jenkinsansibledemo1.git'
            }
        }
        
        stage('Build'){
            steps{
                sh "mvn clean package"
                sh "docker build . -t devopsforyou94/Helloapp:${DOCKER_TAG} "
                withCredentials([string(credentialsId: '3168735c-0100-4554-806f-3ef1e6b3e3a6', variable: 'dockerHubPwd')]) {
                    sh "docker login -u devopsforyou94 -p ${dockerHubPwd}"
                }
                
                sh "docker push devopsforyou94/Helloapp:${DOCKER_TAG} "
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
