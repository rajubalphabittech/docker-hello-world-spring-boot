node {
    // reference to maven
    // ** NOTE: This 'maven-3.6.1' Maven tool must be configured in the Jenkins Global Configuration.   
    def mvnHome = tool 'maven-3.8.1'
	
    // environemtn variables
	environment {
		imageName = "hello-world-java"
		registryCredentials = "nexus"
		NexusDockerRegistryUrl = "http://10.171.14.84:8081/repository/jenkinstest/"
		dockerImage = ''
	}

    // holds reference to docker image
    def dockerImage
    // ip address of the docker private repository(nexus)
    
    def dockerRepoUrl = "localhost:8083"
    def dockerImageName = "hello-world-java"
    def dockerImageTag = "${dockerRepoUrl}/${dockerImageName}:${env.BUILD_NUMBER}"
    
    stage('Clone Repo') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/rajubalphabittech/docker-hello-world-spring-boot.git'
      // Get the Maven tool.
      // ** NOTE: This 'maven-3.6.1' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'maven-3.8.1'
    }    
  
    stage('Build Project') {
      // build project via maven
      sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
    }
	
	stage('Publish Tests Results'){
      parallel(
        publishJunitTestsResultsToJenkins: {
          echo "Publish junit Tests Results"
		  junit '**/target/surefire-reports/TEST-*.xml'
		  archive 'target/*.jar'
        },
        publishJunitTestsResultsToSonar: {
          echo "This is branch b"
      })
    }
		
    stage('Build Docker Image') {
      // build docker image
      sh "sudo whoami"
      sh "sudo ls -all /var/run/docker.sock"
      sh "sudo mv ./target/hello*.jar ./data" 
      
      dockerImage = docker.build("hello-world-java")
    }
   
    stage('Deploy Docker Image'){
      
      // deploy docker image to nexus

      echo "Docker Image Tag Name: ${dockerImageTag}"
      //dockerImage = docker.build imageName
	//    docker.withRegistry( 'http://'+registry, registryCredentails ){
      //      dockerImage.push('latest')
      sh 'sudo docker login -u admin http://10.171.14.84:8086/repository/hello-world-java:latest/'
      sh 'sudo docker push 10.171.14.84:8086/repository/hello-world-java:latest '
	    
      //sh 'docker rmi $(docker images --filter=reference="NexusDockerRegistryUrl/ImageName*" -q)'
      //sh 'docker logout NexusDockerRegistryUrl'
	    //}
    stage('Deploy k8'){
      sh 'kubectl delete -f kubedeploy.yml'
      //sh 'sudo docker login -u admin http://10.171.14.84:8086/repository/hello-world-java:latest/'
      //sh 'sudo docker pull 10.171.14.84:8086/repository/hello-world-java:latest '
	   // kubernetesDeploy{
	//	    configs: "kubedeploy.yml",
	 //           kubeconfigId: "kubernetes_cluster_config",
	 //           enableConfigSubtitution: true
	    }
	    
      //sh 'docker rmi $(docker images --filter=reference="NexusDockerRegistryUrl/ImageName*" -q)'
      //sh 'docker logout NexusDockerRegistryUrl'
	    //}

      //sh "docker login -u admin -p admin123 ${dockerRepoUrl}"
      //sh "docker tag ${dockerImageName} ${dockerImageTag}"
      //sh "docker push ${dockerImageTag}"
    }
}
