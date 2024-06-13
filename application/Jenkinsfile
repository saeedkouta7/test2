@Library('shared_library')_
pipeline {
    agent any
    
    environment {
	SCANNER_HOME                = tool 'sonar-scanner'
        dockerHubCredentialsID = 'DockerHub'                          // DockerHub credentials ID.
        imageName              = 'saeedkouta/ivolve-test1'            // DockerHub repo/image name.
        openshiftCredentialsID = 'openshift-token'                    // Service account token credentials ID
        openshiftClusterURL    = 'https://api.ocp-training.ivolve-test.com:6443' // OpenShift Cluster URL.
        openshiftProject       = 'saeedkouta'                         // OpenShift project name.
    }

    stages {
        stage('Repo Checkout') {
           steps {
            	script {
                	gitcheckout
                }
            }
        }

        stage('Run Unit Test') {
            steps {
                script {
                	// Navigate to the directory contains the Application
                	dir('application') {
                		unitTests
            		}
        	   }
    	    }
	    }

        stage('Run Code Compile') {
            steps {
                script {
                	// Navigate to the directory contains the Application
                	dir('application') {
                		codecompile
            		}
        	    }
    	    }
	    }

        stage('Run SonarQube Analysis') {
            steps {
                script {
                    	// Navigate to the directory contains the Application
                    	dir('application') {
                    		sonarQubeAnalysis()
                    	}
                    }
                }
            }

        
        stage('Build & Push Docker Image') {
            steps {
                script {
                    dir('application') {
                        // Navigate to the directory that contains Dockerfile
                        buildAndPushDockerImage(dockerHubCredentialsID, imageName, BUILD_NUMBER)
                    }
                }
            }
        }

        stage('Deploy on OpenShift Cluster') {
            steps {
                script {
                    dir('application') {
                        deployToOpenShift(openshiftCredentialsID, openshiftClusterURL, openshiftProject, imageName, BUILD_NUMBER)
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed."
            cleanWs() // Clean workspace after the build is finished
        }
        success {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline succeeded"
        }
        failure {
            echo "${JOB_NAME}-${BUILD_NUMBER} pipeline failed"
        }
    }
}
