pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'calm-seeker-375715'
                CLUSTER_NAME = 'staging'
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
	          echo "Testing in develop..."
		  sh 'mvn test'
		}
	   }
	    /* stage('Static code scan & Quality Gate Status') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: "${sonar_cred}") {
                        sh "${code_analysis}"
                    }
                    waitForQualityGate abortPipeline: true, credentialsId: "${sonar_cred}"
                }
            }
        } 
		stage('Upload Artifact to nexus') {
            steps {
                script {
                    
                    def mavenpom = readMavenPom file: 'pom.xml'
                  
                    nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'k8sDemo',
                        classifier: '',
                        file: "target/k8sDemo-${mavenpom.version}.war",
                        type: 'war'
                    ]
                ],
                    credentialsId: "${env.nex_cred}",
                    groupId: "${env.grp_ID}",
                    nexusUrl: "${env.nex_url}",
                    nexusVersion: "${env.nex_ver}",
                    protocol: "${env.proto}",
                    repository: 'nex_repo',
                    version: "${mavenpom.version}"
                    echo 'Artifact uploaded to nexus repository'
                }
            }
        } */
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
	   
           stage('Deploy to staging') { 
		   when {
                branch 'develop'
            }
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
