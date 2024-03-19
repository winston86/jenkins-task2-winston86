pipeline {
    agent any
    environment { 
		APP_PORT=9090
		job_name="${env.JOB_NAME}"
	}
    // Save the job name in a global variable
    stages {
		stage('Clone') {
            steps {
                git url: 'https://github.com/winston86/jenkins-task2-winston86.git', 
		        branch: 'main',
		        credentialsId: 'github_creds'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Integration Test') {
            // Run the following stages in parallel
			parallel {
				stage('Running Application') {
					agent any
					// Set 60 seconds to complete the task
					options {
						timeout(time: 60, unit: "SECONDS")
					}
					steps {
						// Open the script block
						script {
							// Open the try block
							try {
								// Use the dir("TODO") { Commands } construct to return to the target folder
								// Run the "contact.war" application from the "target" folder
								dir("../${job_name}/target") {
 									sh "java -jar contact.war"
								}
							// Open the catch block
							} catch (Throwable e) {
								echo "Caught ${e.toString()} in \"${job_name}\" Job"
								// Return "success" if the task is stopped after 60 seconds
								currentBuild.result = "success"
							// End of try-catch block
							}
						// End of the script block
						}						
					}
				}
				stage('Running Test') {
					steps {
						// Wait 30 seconds for "contact.war" application to run
						sleep(time: 30, unit: "SECONDS")
						// Run only the "RestIT" integration test in the "test" phase of maven
						sh 'mvn -Dtest=RestIT test'
					}
				}
            // End of parallel stages
			}
        }        
    }
}
