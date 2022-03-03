pipeline {
    agent {
        node {
            label 'slave'
        }
    }
    parameters {
	string(name: 'imageTag', defaultValue: 'latest', description: 'Enter Docker Image tag')
	password(name: 'dockerpass', defaultValue: 'Rahul#143', description: 'Enter docker login password ')
	string(name: 'targetserver', defaultValue: 'j-slave2-CT', description: 'Enter Target server name ')
	string(name: 'targetserverIP', defaultValue: '15.207.111.16', description: 'Enter Target Server IP ')
    }
    stages {
        stage('SCM checkout'){
            steps {
		git "https://github.com/devopsbymat/devopsIQ.git"
            }
	}
	stage('Remove dockers'){
	    steps {
		sh "if [ `sudo docker ps -a -q|wc -l` -gt 0 ]; then sudo docker rm -f \$(sudo docker ps -a -q);fi"
		}
	}
	stage('Build'){
	    steps {
		    sh "sudo docker build /home/ubuntu/workspace/${JOB_NAME} -t rganjaredocker/devops-ct:${imageTag}"
	   }
	}	
	stage('Docker Push'){
		steps {
		    sh "sudo docker login --username rganjaredocker --password ${dockerpass}"
                    sh "sudo docker push rganjaredocker/devops-ct:${imageTag}"
	        }
	}
	stage('Configure servers with Docker and deploy website') {
            	steps {
			sh 'ansible-playbook docker.yaml -i inventory_aws_servers.txt'
            	}
        }
	stage('Install Chrome browser') {
            	steps {
                	sh 'ansible-playbook chrome.yaml -e "hostname=localhost"'
            	}
        }
	stage ('Testing'){
		steps {
			sh "sudo apt install python3-pip -y"
			sh "pip3 install selenium"
			sh "python3 selenium_test.py ${params.targetserverIP}"
		}
	}
    }
}
