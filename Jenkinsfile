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

        // stage('Quality Gate') {
        //     steps {
        //         timeout(time: 3, unit: 'MINUTES') {
        //             waitForQualityGate abortPipeline: true
        //         }   
        //     }
        // }

        // stage ('Build Docker Image') {
        //   steps {
        //     // script{
        //     //  dockerHome= tool 'docker-inst'
        //     // }
        //     //  sh "${dockerHome}/bin/docker build . -t clixx-image:$VERSION "
        //     sh '''
        //     docker build . -t clixx-image:$VERSION
        //     docker images
        //     '''
        //   }
        // }

        // stage ('Creating Docker Container Image') {
        //   steps {
        //       sh '''
        //       if ( docker ps | grep clixx-cont ) then
        //          echo "Docker image exists, killing it"
        //          docker stop clixx-cont
        //          docker rm clixx-cont
        //          docker run --name clixx-cont -p 80:80 -d clixx-image:$VERSION
        //          docker ps
        //       else
        //          docker run --name clixx-cont  -p 80:80 -d clixx-image:$VERSION 
        //          docker ps
        //       fi
        //       '''
        //   }
        // }

        // stage ('Restore CliXX Database') {
        //   steps {    
        //     withCredentials([string(credentialsId: 'access_key', variable: 'access_key'),string(credentialsId: 'secret_access_key', variable: 'secret_access_key')]){        
        //     sh '''
        //     python3 -m venv python3-virtualenv
        //     source python3-virtualenv/bin/activate
        //     pip3 install boto3 botocore boto
        //     ansible-playbook -i localhost $WORKSPACE/deploy_db_ansible/deploy_db.yml --extra-vars "access_key=${access_key} secret_key=${secret_access_key}"
        //     deactivate
        //     '''
        //   }
        // }
        // }

        // stage ('Configure DB Instance') {
        //   steps {
        //     withCredentials([string(credentialsId: 'DB_USER', variable: 'DB_USER_VAR'), string(credentialsId: 'DB_PASSWORD', variable: 'DB_PASSWORD_VAR'), string(credentialsId: 'DB_NAME', variable: 'DB_NAME_VAR'), string(credentialsId: 'DB_HOST', variable: 'DB_HOST_VAR')]){
        //     script {
        //     def userInput = input(id: 'confirm', message: 'Is DB creation complete?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Complete?', name: 'confirm'] ])
        //      }
        //       sh '''
        //        USERNAME=$DB_USER_VAR
        //        PASSWORD=$DB_PASSWORD_VAR
        //        DBNAME=$DB_NAME_VAR
        //        SERVER_INSTANCE=$DB_HOST_VAR              

        //        TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
        //        SERVER_IP=`curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/public-ipv4`   
               
        //        #OR
        //        #SERVER_IP=$(curl -s http://checkip.dyndns.org | sed -e 's/.*Current IP Address: //' -e 's/<.*$//')            
               
        //        echo "use wordpressdb;" > $WORKSPACE/db.setup
        //        echo "UPDATE wp_options SET option_value = '$SERVER_IP' WHERE option_id = '1';" >> $WORKSPACE/db.setup
        //        echo "UPDATE wp_options SET option_value = '$SERVER_IP' WHERE option_id = '2';" >> $WORKSPACE/db.setup

        //        mysql -u $USERNAME --password=$PASSWORD -h $SERVER_INSTANCE -D $DBNAME < $WORKSPACE/db.setup
               
        //        #docker exec -d clixx-cont sed -i "s/'wordpressdbclixxjenkins.cd7numzl1xfe.us-east-1.rds.amazonaws.com'/'${SERVER_INSTANCE}'/g" /var/www/html/wp-config.php
               
        //       '''
        //   }
        // }
        // }

        // stage ('Deployment Destination') {
        // steps {
        //     script {
        //         def userInput = input(id: 'confirm', message: 'Image operates properly?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Tear Down Environment?', name: 'confirm'] ])
        //      }
        //      withCredentials([string(credentialsId: 'DB_HOST', variable: 'DB_HOST_VAR')]){
        //      sh '''
        //       SERVER_INSTANCE=$DB_HOST_VAR
        //       docker exec -d clixx-cont sed -i "s/'wordpressdbclixxjenkins.cd7numzl1xfe.us-east-1.rds.amazonaws.com'/'${SERVER_INSTANCE}'/g" /var/www/html/wp-config.php
        //      '''
        //      }
        //   }
        // }

        // stage ('Change wp-config check') {
        // steps {
        //     script {
        //         def userInput = input(id: 'confirm', message: 'wp-config was changed?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Tear Down Environment?', name: 'confirm'] ])
        //     }
        // }
        // }

        // stage ('Tear Down CliXX Docker Image and Database') {
        //   steps {
        //     script {
        //        def userInput = input(id: 'confirm', message: 'Tear Down Environment?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Tear Down Environment?', name: 'confirm'] ])
        //     }
        //     withCredentials([string(credentialsId: 'access_key', variable: 'access_key'),string(credentialsId: 'secret_access_key', variable: 'secret_access_key')]){
        //     sh '''
        //       python3 -m venv python3-virtualenv
        //       source python3-virtualenv/bin/activate
        //       python3 --version
        //       pip3 install boto3 botocore boto
        //       ansible-playbook $WORKSPACE/deploy_db_ansible/delete_db.yml
        //       deactivate
        //       docker stop clixx-cont && docker rm  clixx-cont
              
        //       '''
        //     }
        //   }
        // }

        // stage ('Log Into ECR and push the newly created Docker') {
        //   steps {
        //      script {
        //         def userInput = input(id: 'confirm', message: 'Push Image To ECR?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Push to ECR?', name: 'confirm'] ])
        //      }
        //      withCredentials([string(credentialsId: 'ECR_USERNAME', variable: 'ECR_USERNAME_VAR'), string(credentialsId: 'ECR_REPO', variable: 'ECR_REPO_VAR'), ]){
        //       sh '''
        //         aws ecr get-login-password --region us-east-1 | docker login --username ${ECR_USERNAME_VAR} --password-stdin ${ECR_REPO_VAR}
        //         docker tag clixx-image:$VERSION ${ECR_REPO_VAR}:clixx-image-$VERSION
        //         docker tag clixx-image:$VERSION ${ECR_REPO_VAR}:latest

        //         docker push ${ECR_REPO_VAR}:clixx-image-$VERSION
        //         docker push ${ECR_REPO_VAR}:latest

        //         '''
        //      }
        //   }
        // }

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

// def getAnsiblePath(){
//         def AnsibleHome= tool name: 'ansible-pb', type: 'ansiblePlaybook installation: 'ansible-pb', playbook: '', vaultTmpPath: '''
//         return AnsibleHome
//     }
