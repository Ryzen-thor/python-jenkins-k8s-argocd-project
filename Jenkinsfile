pipeline { 
	agent any 
	
	environment  {
		IMAGE_TAG = "${BUILD_NUMBER}"
		// APP_NAME = "TODO-APP
	}

	stages {

	  stage('Checkout') {
	     steps {
	       git branch: 'main', url: 'https://github.com/Ryzen-thor/python-jenkins-k8s-argocd-project.git'
	      }

	     }

	  stage('Build Docker'){
		steps{
			script{
				sh '''
				echo 'Build Docker Image'
				docker build -t stormbreaker2023/todo-app:${BUILD_NUMBER} .
				'''
			}

		}
		
	  }

	  stage('Push the artifacts'){
		steps{
			
					echo 'push to DockerHub'
     					withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerPass",usernameVariable:"dockerUser")]){
	  			        sh "docker login -u ${env.dockerUser} -p ${env.dockerPass}"
					sh "docker push ${env.dockerUser}/todo-app:${BUILD_NUMBER}"
				
			}
		}
	  }

	  stage('Checkout K8S manifest SCM'){
		steps{
			git credentialsId: 'gitCred',
			url: 'https://github.com/Ryzen-thor/manifest-file-argocd-project.git',
			branch: 'main'
		}
	  }

	  stage('Update k8s manifest and push to repo'){
		steps{
			
				sh """
    				    
    				    sed -i "s/todo-app.*/todo-app:${BUILD_NUMBER}/g" deployment.yaml
		                   
		      	   	   
		                    git add deployment.yaml
		                    git commit -m "Updated Deployment Manifest"
		      		    
		                """
			
				withCredentials([gitUsernamePassword(credentialsId: 'gitCred',gitToolName: 'Default')]) {
                    		sh "git push https://github.com/Ryzen-thor/manifest-file-argocd-project.git main"
                }
				
				
			
		
	  }
	 }

}
}
