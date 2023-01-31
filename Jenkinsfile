pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'calm-seeker-375715'
                CLUSTER_NAME = 'gke'
                LOCATION = 'us-central1-c'
                CREDENTIALS_ID = 'My First Project'		
	}
	
    stages {	
	   stage('Scm Checkout') {            
		steps {
                  checkout scm
		}	
           }
           
	   stage('Build in develop') { 
                steps {
                  echo "Cleaning and packaging..."
                  sh 'mvn clean package'
                }
           }
	    
	   stage('Test in develop') { 
		steps {
	          echo "Testing..."
		  sh 'mvn test'
		}
	   }
	   stage('Build Docker Image in develop') { 
		steps {
		   sh 'whoami'
                   script {
		      myimage = docker.build("rishi236/devops:${env.BUILD_ID}")
                   }
                }
	   }
	   stage("Push Docker Image in develop") {
                steps {
                   script {
                      docker.withRegistry('https://registry.hub.docker.com', 'docker') {
                      myimage.push("${env.BUILD_ID}")
                     }   
                   }
                }
            }
	   
           stage('Deploy to GKE in staging') { 
                steps{
                   echo "Deployment started ..."
		   sh 'ls -ltr'
		   sh 'pwd'
		   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                   step([$class: 'KubernetesEngineBuilder', 
			 projectId: env.PROJECT_ID, 
			 clusterName: env.CLUSTER_NAME,
			 location: env.LOCATION, 
			 manifestPattern: 'deployment.yaml',
			 credentialsId: env.CREDENTIALS_ID, 
			 verifyDeployments: true])
		   echo "Deployment Finished ..."
            }
	   }
    }
}
