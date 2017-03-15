pipeline {
	// Specify the agent to use in execution of the pipeline
    agent any

	// 
    environment { 
        DOCKER_IMG_NAME = "demo-app:0.0.1"
    }
    
    // 
    options {
    	// Keep the 10 most recent builds
    	buildDiscarder(logRotator(numToKeepStr:'10')) 
  	}

	stages {
	
		stage('Start Up') {
			steps {
				sh 'printenv'
			}
		}
	
		stage('Checkout Code') {
			steps {
				git 'https://github.com/cvitter/sample-rest-server'
			}
		}
		
		stage('Building') {
			steps {
				// Build our code using Maven from the command line
				//   - package flag builds our jars and runs unit tests
				//   - site flag runs PMD and builds our static analysis report
				timeout(time: 5, unit: 'MINUTES') {
                	sh './slow-process.sh'
                }
			}
			post {
				success {
					// Archives the jar uber jar file we created
					archiveArtifacts 'target/*with-dependencies.jar	'
					
					// Publish PMD analysis
					step([$class: 'PmdPublisher', pattern: 'target/pmd.xml'])
				
					// Publish Junit test reports
					junit 'target/surefire-reports/*.xml'
				}
			}
		}

		
		stage('Build Docker Image') {
			steps {
				echo "Docker Image Name: ${DOCKER_IMG_NAME}"
			}
		}
		
    }

}