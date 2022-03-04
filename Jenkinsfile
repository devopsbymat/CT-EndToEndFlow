pipeline {
    agent {
        node {
            label 'devOps-master'
        }
    }
    parameters {
	string(name: 'imageTag', defaultValue: 'apache-latest', description: 'Enter Docker Image tag')
	password(name: 'dockerpass', defaultValue: 'Rahul#143', description: 'Enter docker login password ')
	string(name: 'targetserver', defaultValue: 'j-slave2-CT', description: 'Enter Target server name ')
	string(name: 'targetserverIP', defaultValue: '15.207.111.16', description: 'Enter Target Server IP ')
    }

    stages {
    stage('SCM checkout'){
        steps {
		git "https://github.com/devopsbymat/DevOps-EndToEndFlow.git"
            }
	}

	stage ("install docker, curl & https & check docker version"){
	  steps{
	     sh "sudo apt-get update"
             sh "sudo apt-get install docker.io -y" 
             sh "sudo apt-get update && apt-get install -y apt-transport-https curl"
	     sh "docker --version"
		}
	}

	stage('Remove old docker containers if any with same name'){
	    steps {
		sh "if [ `sudo docker ps -a -q|wc -l` -gt 0 ]; then sudo [docker rm -f \$(sudo docker ps -a -q)];"
		}
	}
	stage('Build'){
	    steps {
		    sh "sudo docker build /home/ubuntu/workspace/${JOB_NAME} -t rganjaredocker/devops-e2e:${imageTag}"
	   }
	}	
	stage('Docker Push'){
		steps {
		    sh "sudo docker login --username rganjaredocker --password ${dockerpass}"
            sh "sudo docker push rganjaredocker/devops-e2e:${imageTag}"
	        }
	}

    }
}
