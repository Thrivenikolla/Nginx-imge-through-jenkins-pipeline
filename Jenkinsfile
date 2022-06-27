pipeline {
    agent any
	environment {
        UUID uuid = UUID.randomUUID()
        registryCredential ='docker_cred'
	containerName = "shraddhal/seleniumtest2"
        container_version = "1.0.0.${BUILD_ID}"
        dockerTag = "${containerName}:${container_version}"
        }
	
	tools {
        maven '3.8.5' 
        }
	
    stages {
	    stage('GIT clone repo and creation of version.html') {
            steps {
               //clone repo
               git 'https://github.com/Thrivenikolla/Nginx-imge-through-jenkins-pipeline.git'
			  
	       //Creating version.html and writing randomUUID to it
	       sh script:'''
	       touch musicstore/src/main/webapp/version.html
	       '''
	       println uuid
	       writeFile file: "musicstore/src/main/webapp/version.html", text: uuid
                  }
           }
	    
	   stage('Build maven project'){
		//cd to pom.xml 
		steps{
		   sh script:'''
		   cd musicstore
		   mvn -Dmaven.test.failure.ignore=true clean package
		   '''
		     }
	   }
	    
	  stage('Docker build and publish nginx image'){
		//build nginx image with name nginx using Dockerfile and publish on dockerhub/shivani221	
		steps{
		    script{
			 dockerImage = docker.build("thrivenik/nginx")
			 docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                         dockerImage.push("$BUILD_NUMBER")
                         dockerImage.push('latest')
			 }
			 }
		    }
	  }    
	    
	stage('Running the nginx container') {
		//running nginx container with name nginx using the published image, port is 80
		steps{
	         sh 'docker run -d --name nginx -p 80:80 thrivenik/nginx:latest'
	        }
	 }
        }//stages closed
	
