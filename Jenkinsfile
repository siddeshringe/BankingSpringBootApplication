node{
    
    def tag, dockerHubUser, containerName, httpPort = ""
    
     environment {
        echo 'Initialize Environment'
        tag="1.0"
	withCredentials([usernamePassword(credentialsId: 'DockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
		dockerHubUser="$dockerUser"
        }
	containerName="bankingapp"
	httpPort="8989"
    }
    
    stage('Code Checkout'){
        try{
            checkout scm
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
        }
    }
    
    stage('Maven Build'){
        sh "mvn clean package"        
    }
    
    stage('Docker Image Build'){
	    steps{
        echo 'Creating Docker image'
        sh "docker build -t $dockerHubUser/$containerName:$tag --pull --no-cache ."
    }  
    }
	
    stage('Publishing Image to DockerHub'){
        echo 'Pushing the docker image to DockerHub'
	    steps{
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
		sh "docker login -u $dockerUser -p $dockerPassword"
		sh "docker push $dockerUser/$containerName:$tag"
		echo "Image push complete"
        } 
    }    
    }
	
	stage('Docker Deployment'){
		steps{
		sh "docker rm $containerName -f"
		sh "docker pull $dockerHubUser/$containerName:$tag"
		sh "docker run -d --rm -p $httpPort:$httpPort --name $containerName $dockerHubUser/$containerName:$tag"
		echo "Application started on port: ${httpPort} (http)"
	}
	}
}


