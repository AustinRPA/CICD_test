pipeline {
	    agent any
	

	        // Environment Variables
	        environment {
			PROCESS_NAME ="CICD_test"
	        MAJOR = '1'
	        MINOR = '0'

	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
			UIPATH_ORCH_LOGICAL_NAME = "persortjnbdz"
	        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
	        UIPATH_ORCH_FOLDER_NAME = "Test"
	    }
	

	    stages {
	

	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranchName ${env.BRANCH_NAME}"
	                checkout scm
	

	            }
	        }
	

	         // Build Stages
	        stage('Build') {
	            steps {
	                echo "Building..with ${WORKSPACE}"
	                UiPathPack (
						disableBuiltInNugetFeeds: false, 
						governanceFilePath: '', 
						outputPath: "Output\\${env.BUILD_NUMBER}", 
						outputType: 'Process', 
						projectJsonPath: 'project.json', 
						projectUrl: '', 
						releaseNotes: '', 
						repositoryBranch: '', 
						repositoryCommit: '', 
						repositoryType: '', 
						repositoryUrl: '', 
						splitOutput: false, 
						traceLevel: 'None', 
						version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"]
	        )
	            }
	        }
	

	         // Deploy Stage
	        stage('Deploy to Test') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to Test "

					UiPathDeploy (
					createProcess: true, 
					credentials:Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: '633cc25d-ef31-4240-aff6-86986d367266'), 
					entryPointPaths: 'Main.xaml', 
					environments: '', 
					folderName: "${IPATH_ORCH_FOLDER_NAMEU}", 
					ignoreLibraryDeployConflict: false, 
					orchestratorAddress: "${UIPATH_ORCH_URL}",
					 orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}", 
					 packagePath: "Output\\${env.BUILD_NUMBER}", 
					 processName: "${PROCESS_NAME}", 
					 processNames: '', 
					 traceLevel: 'None'
                     )                    
	            }

			// Run In Test Stage
			stage('Run in Test') {
				UiPathRunJob(

				 credentials: Token(accountName: '${UIPATH_ORCH_LOGICAL_NAME}', 
				 credentialsId: '633cc25d-ef31-4240-aff6-86986d367266'),
				  failWhenJobFails: true, 
				  folderName: "${UIPATH_ORCH_FOLDER_NAME}", 
				  jobType: Test(), orchestratorAddress: "${UIPATH_ORCH_URL}",
				  orchestratorTenant:"${UIPATH_ORCH_TENANT_NAME}", 
				  parametersFilePath: '', 
				  priority: 'Normal', 
				  processName: "${PROCESS_NAME}", 
				  resultFilePath: '', 
				  strategy: Robot('Test Robot'), 
				  traceLevel: 'None',
				  waitForJobCompletion: true
				  )




			}

	        }

	    }
        // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	

	    // 
	    post {
	        success {
	            echo 'Deployment has been completed!'
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        always {
	            /* Clean workspace if success */
	            cleanWs()
	        }
	    }
	
	

	}
