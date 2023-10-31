pipeline {
    agent { label 'dev' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
	    APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "hashirahmad"
            DOCKER_PASS = 'jenkins-token'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	    JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
    }	
	
    
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/Hashirahmad996/register-app'
                }
        }

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

        }

       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }

       stage("SonarQube Analysis"){
           steps {
	           script {
		        withSonarQubeEnv(credentialsId: 'jenkins-sonarkube-token') { 
                        sh "mvn sonar:sonar"
		        }
	           }	
           }
       }
       stage("Quality Gate"){
           steps {
		   sleep(60)
		   timeout(time: 1, unit: 'HOURS') {
			   
                           waitForQualityGate abortPipeline: true, credentialsId: 'jenkins-sonarqube-token'
			   	   
                   }  	
           }
       }

	stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
		}	
	    }
        }    
	    
    }     
   
}
