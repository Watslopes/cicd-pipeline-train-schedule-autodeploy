node {
	def application = "eduproj"
	def dockerhubaccountid = "watslopes2611"
	stage('Clone repository') {
		checkout scm
	}

	stage('Build image') {
		app = docker.build("${dockerhubaccountid}/${application}:${BUILD_NUMBER}")
	}

	stage('Push image') {
		withDockerRegistry([ credentialsId: "dockerHub", url: "" ]) {
		app.push()
		app.push("latest")
	}
	}

	stage('Deploy') {
		withCredentials([file(credentialsId: 'kubeconfig', variable: 'kubecfg')]){
		sh ("kubectl --kubeconfig /home/centos/.kube/config apply -f train-schedule-kube-canary.yml")
		sh ("kubectl --kubeconfig /home/centos/.kube/config apply -f train-schedule-kube.yml")
		}
	}
	
	stage('Remove old images') {
		// remove docker pld images
		sh("docker rmi ${dockerhubaccountid}/${application}:latest -f")
   }
}
