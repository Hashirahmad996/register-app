pipeline {
    agent { label 'dev' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
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
	    
    }     
   
}
