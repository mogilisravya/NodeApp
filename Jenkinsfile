pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
		// DOCKER_HUB_CREDENTIALS_ID = 'jen-dockerhub'
		// DOCKER_HUB_REPO = 'iquantc/iquant-app'
		DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-jenkins-token'
		DOCKER_REGISTRY='https://hub.docker.com/u/sravya291'
		DOCKER_HUB_REPO='sravya291/project-apps'
	}
	stages {
		stage('Checkout Github'){
			steps {
				git branch: 'main', credentialsId: 'jen-doc-git', url: 'https://github.com/mogilisravya/NodeApp.git'
			}
		}		
		stage('Install node dependencies'){
			steps {
				sh 'npm install'
			}
		}
		stage('Test Code'){
			steps {
				sh 'npm test'
			}
		}
		stage('Build Docker Image'){
			steps {
				script {
					//docker.build("nodeimage"+"$BUILD_NUMBER")
					dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
				}
			}
		}
		// stage('Trivy Scan'){
		// 	steps {
		// 		sh 'trivy --severity HIGH,CRITICAL --no-progress image --format table -o trivy-scan-report.txt ${DOCKER_HUB_REPO}:latest'
		// 	}
		// }
		stage('Push Image to DockerHub'){
			steps {
				script {
					docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS_ID}"){
						dockerImage.push('latest')
					}
				}
			}
		}
		stage('Install Kubectl'){
			steps {
				sh '''
				curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
				chmod +x kubectl
				mv kubectl /usr/local/bin/kubectl
				'''
			}
		}
		stage('Deploy to Kubernetes'){
			steps {
				script {
					kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCakNDQWU2Z0F3SUJBZ0lCQVRBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwdGFXNXAKYTNWaVpVTkJNQjRYRFRJMU1UQXdOVEV4TlRnME4xb1hEVE0xTVRBd05ERXhOVGcwTjFvd0ZURVRNQkVHQTFVRQpBeE1LYldsdWFXdDFZbVZEUVRDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTGJyCjhRT0dzc0NQVjBzdjQveE1pSHFkcUVkRHZPUXZ0UTE1RzM0V2NmQXV4Q0hvMWJnamhFU21GNlZyK2xKOWpmSncKcTFnRlBDTWpQRzZNZGJlUFpENEdLck05K3ZzRE5CbDZ5WnkzY1NadlZLRHRJVzIzYktMdkNKTzZGbzFqdStMMwo1TGRjekdmcS90ZzBvRFNRc2hDVDl2UFlJV3o0Uk5SZjF2aFl4Ni9PaTVZUS9ucVJEcVZ1T0R5YTlmcFlVT3lQCitBVFhCN01ZZzl3a0t2OEJpa3NZWmQ0YnZRUFhVRndsZnhuVFFyNE1sSGZ4dnExb0laTE0xR0o2UktVMlBKVlAKY3hMS1Z0RmFjOFJHTXhYUFJCSGdIZkRRZFgrTjljbk0rcU5CSmVpYVRKK0lmWGpXeXV5azNDT3NBaThlcjQ5RwpNK0N5ak1ZcHlGZ2Zqa2FwcllFQ0F3RUFBYU5oTUY4d0RnWURWUjBQQVFIL0JBUURBZ0trTUIwR0ExVWRKUVFXCk1CUUdDQ3NHQVFVRkJ3TUNCZ2dyQmdFRkJRY0RBVEFQQmdOVkhSTUJBZjhFQlRBREFRSC9NQjBHQTFVZERnUVcKQkJROExpTmFxb3pmTlgxc0hSRWZDcWJhYmdaQ05UQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFzSjVWbGdFdQpSaEFBak8zdmZWaE1QQ0tUNlZRRFJxYW9ZSmdpdjZZQWxhOUl3UVgyV05YdHlqVVVNakRaUFQrZWJrU0hPNjJlCitlbjFXWGdVWW1mV0pmOUEycC9HY0ROaHg3SCtwdUt0bVk4aFloZFZUc0xkTTNTM0htb1M3SEg1NmNBdVlLbFQKaEY2V1B0ZURMM0tlVTdHQzg4Y3RCSTFBaVR0S0pGelQ5eWVScHM4NHRCaDFTbzdBcDR3bG9GWWpXNy9FUTFrVgplUkZGTE5zRmhaNlRlVGpnSGwzT0p0L01jV2hUbytyQWtTRnRtOEFoNlRjOU1MWTBacis1OTlwemNCU2ZZN0s4Ck9sa0R2RFdtd09ORUhiTzFSa1QyQjUyWHlUZXlYSHgzaDZKUTRpWk9vaGR4U2lNN2lGVk05dTBRaUorWDdmL2kKd2tQSC8wRTM2UzVBMHc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==', credentialsId: 'kubeconfig', serverUrl: 'https://127.0.0.1:57738') {
						sh 'kubectl apply -f deployment.yaml'
					}
				}
			}
		}
	}

	post {
		success {
			echo 'Build&Deploy completed succesfully!'
		}
		failure {
			echo 'Build&Deploy failed. Check logs.'
		}
	}
}









