pipeline {
    agent any
    environment{
    DOCKER_TAG = getDockerTag()
  }

      stages {
           stage ('Checkout SCM'){
             steps {
          checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/automateops123/ReactJs-tour.git']]])
             }
        
      }     

      stage("Build"){
          steps{
              sh "npm install"
              sh "docker build -f Dockerfile /var/lib/jenkins/workspace/DevOps-COE-POC-Pipeline-Jobs/ReactJS -t saikumar080319/react:${DOCKER_TAG}" 
          }
    
      }

      stage("Docker login & Push"){
          steps{
          withCredentials([string(credentialsId: 'Docker', variable: 'Dockercred')]) {
              sh "docker login -u saikumar080319 -p ${Dockercred} "
               }
              
             sh "docker push saikumar080319/react:${DOCKER_TAG}"
          }
      }

      stage("Deploy"){
          steps{ sshagent(['ssh_keys']) {
              sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.29.88 docker rm -f cont1 || true"
             sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.29.88 docker run -itd -p 80:8080 --name cont1 saikumar080319/react:${DOCKER_TAG} ."
             
           }
        }
      }   
    }
}


def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
