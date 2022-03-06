
def gitURL = 'https://github.com/devopsbymat/DevOps-EndToEndFlow.git'
def branchName = 'master'

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
    stage('SCM Checkout'){
        steps {
            checkout([$class: 'GitSCM', branches: [[name: "*/${branchName}"]], extensions: [], userRemoteConfigs: [[credentialsId: 'github-Cred', url: "${gitURL}"]]])
        }
    }

    stage ("Install Docker"){
        steps{
            sh "sudo apt-get update"
            sh "sudo apt-get install docker.io -y" 
            sh "sudo apt-get update" 
            sh "sudo apt-get install -y apt-transport-https curl"
            sh "docker --version"
        }
    }

    stage('Clean Containers'){
        steps {
            sh "if [ `sudo docker ps -a -q|wc -l` -gt 0 ]; then sudo [docker rm -f \$(sudo docker ps -a -q)];fi"
        }
    }
    stage('Build'){
        steps {
           sh "sudo docker build /home/ubuntu/jenkins/workspace/${JOB_NAME} -t rganjaredocker/devops-e2e:${imageTag}"
        }
    }
    stage('Docker Push'){
        steps {
          sh "sudo docker login --username rganjaredocker --password ${dockerpass}"
          sh "sudo docker push rganjaredocker/devops-e2e:${imageTag}"
        }
    }
    
	stage('Install K8S'){
		steps{
			sh "sudo ansible -i hosts all -m ping"
			sh "sudo ansible-playbook -i hosts install-kubernetes.yaml"
		}
	}

	stage('Spin-up K8S Cluster'){
		steps{
			sh "sudo ansible-playbook -i hosts spinup_k8s_cluster.yaml"
		}
	}

	}
}
