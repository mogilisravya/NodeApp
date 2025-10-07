pipeline {
	agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
		DOCKER_HUB_CREDENTIALS_ID = 'jen-dockerhub'
		// DOCKER_HUB_REPO = 'iquantc/iquant-app'
		//DOCKER_HUB_CREDENTIALS_ID = 'dockerhub-jenkins-token'
		DOCKER_REGISTRY='https://hub.docker.com/u/sravya291'
		DOCKER_HUB_REPO='sravya291/project-apps'
		//DOCKER_HUB_REPO='sravya291/project'
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
					kubeconfig(caCertificate: 'LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb3dJQkFBS0NBUUVBdDUwVWgwRElsRUdVdVFtbjNScWVYQmZ6UVZYcUJjZVVOK3N3SncvVzBhNkh0alZ6CjBRbTBLTG0wYWk4aFRhd3ZiRXR5RlIzS2I3aVFMQkVVVi9tRUNsUW9teFFmM1VhY1JDTUNielRoVEJwVDlvcncKeGgvMUZBRURYNnA5dEtXWG1uUGdKc3ErQkwrTXZacCtJeXdtNjA2Q3Z6QlBlTWNaOGpHVjNoSDEraTk3OStucgpDaTJWa1FXRlRsK2pSUThVS1NVNDRKQlpMQVZvb1hZYlhzTU5xUjFDRWNoNGpNZkF0Yk9uaFRhWGJkNVlXdFYrCmtoby8zaTJhZ2lEdEUvQWpRMUowekZXTVVjZExKUDAyd3RzOE9FSXQ4VUJ4aGVXZG05Z2xsUXp1QTdKemJLSFUKcjVtaWpZZ2NoT1lKN0g2YTFOSkk0WW9kRG9CbUZTR1ErL2s3YVFJREFRQUJBb0lCQUJ4blJqZXVBOWs3VStCVAo2QTNDQW1GSXF5NmVOYmR6S2pDRVIweWpuSjJQSEJ0SnBZTGV0Mm5kWUZWVXVSZ2szS2twK3BuZG8rVW9iZFVEClIxeGNrdHB1TG1IZStSYm54MkNhOGROMklLdi9IcUh5MkZBdHZXRzh6bUxNSXVvQSs3UyszQnZrSFFUckJNQ0gKMTU5bFF4Zi9heVpjT003OExiMVRnbFY1L2MwWHJFQUlSZE1qWjVxTFh5ek1jWlp5NUlvbDNjQ2FXVDhvZGdLVwpLQUhQR00ycnhVcjZkYVRZZDVHcnh0dDhWUUc2akVIN2Zubm9LdFZaVVcxNS9hUzNneDFoTnNCSnU1aFBXM1I1CkoyRk5hU0R0b0xIZVFhQnVQQmlpWmlDYnMxREhOSWZOeS83Wi84QXFUNVV2Tk51OGdKbWg5a3N2WmU4N2E2QWcKTjVEYng0a0NnWUVBMGxZcGlxUGwyemlPVUhMVVJQWjZqL1IxeFFBdjJ5b0NtOVA5MFdtVW9JNUFmUjVWeGdVUwpEQ3BQT0lLdllnRzVsRDJKZlgrQTRYcWptTktKSUF3N2c1UVNJSGw4RHpvWDZqS0wxNXhXdkFzTVpVaDNEb1BUCk16ekwrK3JQNmtaR3hxNlV2K3BQSTBPREc5VXdBbno3ZFYvNXRyVTAzU2VMaW15QTZFbWZiLzhDZ1lFQTMzbTkKWm9aNFYvZUdrUVY0dVJiREd4VXBSTDdLTXV3Wm1RK2Y2emNDYkMwdU5Oako1b1ByVkR0UG5PK1BPZXBwbWlJTQo2Zzd5Vmg1WW1GRUQ4ejQ0UFM1Yjg0V1h5WUNvS2FhRmVjRUpkdUFma202SVdnQk96Y296MXNPWVlHYWtxT0R3CndRVXRjVmlkQmZPUk5oT2M1UFhxNTh3aDZRVmdKOGM4aE9uUjFKY0NnWUFWVGVEYlNBcUtEOHBxWDVjY2ZSS2cKN2hQWHBsTXlrYWk5KzlUamx4d1V2eFBOR0h0N21SdmcyNUEybXhZRWlVUXd2YUxsY1RxdW0rdGlHVUVHVy9iVApOdlkzRDliKys5VmNSVTVnd2xjT3FOZlNrbXBzci9GK2FSUUlLaHB4RWprcytwWGFjbk1ka1VNQVdMR0tMdnFSClF1MWFFalNPaWVqYVZtOGdHQVRzSlFLQmdFK21GaWs2ZUIzeUxOWDNPMFhNcFJFOEE0OXE2S29wbUI0RGdaM1YKNTFraTZUc1FvVFUxZzJSc0Z6MzU2L1JhWmZUM2VkN2FJT2tzdTZyZUJTaG0zcWgvbzJveURSMVdhNWtjcnFucwpjVEViUkdWdVc4Nm9HVzRqU1VLY1Iremo2SUFBWEE2UGg5akVHb01LZ3BEaGdLUGlPaTBQaG02RTZmYURma0JwCkRjNVZBb0dCQUtLZ0JRSFNrbHI1TWhjbm5kS1FENG54bXZXZXg4YTVJSGhmMFpIWGQ1SkdZSWJUQkhIZmdYQisKSW9Ud1lBSXRlMXpJd2NaYkdEOEpXYzQ2L1JMNUxxa0RvOU9QRzF5ckpxTGxGVHIweGRMZU9VQitVU2tzMVdnZgp5RkZjc0paMzk4V3ZSMHJKOCtScTJqR3Z4VVpoWDRMS2p1WjNZdmRZaE5SaGxYSlRDQXcwCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==', credentialsId: 'kubeconfig', serverUrl: 'https://127.0.0.1:52925') {
						sh 'kubectl apply -f deployment.yaml'
					}
				}
			}
		}
		// stage('Deploy to Kubernetes'){
		// 	steps {
		// 		script {
		// 			withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_BASE64')]) {
		// 				sh '''
		// 				echo $KUBECONFIG_BASE64 | base64 --decode > kubeconfig.yaml
		// 				export KUBECONFIG=$(pwd)/kubeconfig.yaml
		// 				kubectl apply -f deployment.yaml
		// 				kubectl get pods
		// 				'''
		// 			}
		// 		}
		// 	}
		// }

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




















