pipeline {
    agent any

    environment {
        VERSION = "1.0.${BUILD_NUMBER}"
        PATH = "${PATH}:${getSonarPath()}:${getDockerPath()}"
    }
 
    stages {
        stage ('Sonarcube Scan') {
        steps {
            script {
            scannerHome = tool 'sonarqube'
            }

            // use the withCredentials() Jenkins function to introduce the SONAR_TOKEN secret 
            withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]){
            withSonarQubeEnv('SonarQubeScanner') {
            sh " ${scannerHome}/bin/sonar-scanner \
            -Dsonar.projectKey=Activiti-app-Isaac  \
            -Dsonar.login=${SONAR_TOKEN} "
            }
            }
        }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }   
            }
        }

        stage ('Build Docker Image') {
          steps {
            sh '''
            docker build . -t activiti-img:$VERSION
            docker images
            '''
          }
        }

        stage ('Creating Docker Container') {
          steps {
              sh '''
              if ( docker ps | grep activiti-cont ) then
                 echo "Docker image exists, killing it"
                 docker stop activiti-cont
                 docker rm activiti-cont
                 docker run --name activiti-cont -p 8081:8080 -d activiti-img:$VERSION
                 docker ps
              else
                 docker run --name activiti-cont  -p 8081:8080 -d activiti-img:$VERSION 
                 docker ps
              fi
              '''
          }
        }

        stage ('Restore Activiti App MySQL Database and assign Elastic IP "34.203.95.105" to instance') {
          steps {    
            withCredentials([string(credentialsId: 'access_key', variable: 'access_key'),string(credentialsId: 'secret_access_key', variable: 'secret_access_key')]){        
            sh '''
            python3 -m venv python3-virtualenv
            source python3-virtualenv/bin/activate
            pip3 install boto3 botocore boto
            ansible-playbook -i localhost $WORKSPACE/deploy_db_ansible/deploy_db.yml --extra-vars "access_key=${access_key} secret_key=${secret_access_key}"
            deactivate
            '''
          }
        }
        }

        stage ('Deployment Destination') {
        steps {
            script {
                def userInput = input(id: 'confirm', message: 'Verify that image operates properly', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Tear Down Environment?', name: 'confirm'] ])
             }
          }
        }

        stage ('Tear Down Activiti Docker Container and Database') {
          steps {
            script {
               def userInput = input(id: 'confirm', message: 'Tear Down Environment?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Tear Down Environment?', name: 'confirm'] ])
            }
            withCredentials([string(credentialsId: 'access_key', variable: 'access_key'),string(credentialsId: 'secret_access_key', variable: 'secret_access_key')]){
            sh '''
              python3 -m venv python3-virtualenv
              source python3-virtualenv/bin/activate
              python3 --version
              pip3 install boto3 botocore boto
              ansible-playbook -i localhost $WORKSPACE/deploy_db_ansible/delete_db.yml --extra-vars "access_key=${access_key} secret_key=${secret_access_key}"
              deactivate
              docker stop activiti-cont && docker rm activiti-cont
              
              '''
            }
          }
        }

        stage ('Log Into ECR and Push the Newly Created Docker Image') {
          steps {
             script {
                def userInput = input(id: 'confirm', message: 'Push Image To ECR?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Push to ECR?', name: 'confirm'] ])
             }
             withCredentials([string(credentialsId: 'ECR_USERNAME', variable: 'ECR_USERNAME_VAR'), string(credentialsId: 'ECR_REPO_ACTIVITI', variable: 'ECR_REPO_VAR'), ]){
              sh '''
                aws ecr get-login-password --region us-east-1 | docker login --username ${ECR_USERNAME_VAR} --password-stdin ${ECR_REPO_VAR}
                docker tag activiti-img:$VERSION ${ECR_REPO_VAR}:activiti-img-$VERSION
                docker tag activiti-img:$VERSION ${ECR_REPO_VAR}:latest

                docker push ${ECR_REPO_VAR}:activiti-img-$VERSION
                docker push ${ECR_REPO_VAR}:latest

                '''
             }
          }
        }
    }
}

// define a function that references the tool 
def getSonarPath(){
        def SonarHome= tool name: 'sonarqube', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
        return SonarHome
    }

def getDockerPath(){
        def DockerHome= tool name: 'docker-inst', type: 'dockerTool'
        return DockerHome
    }

