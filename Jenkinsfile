pipeline {
	    agent any
	

	        // Environment Variables
	        environment {
	        MAJOR = '1'
	        MINOR = '0'
	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/persortjnbdz"
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
						projectJsonPath: '${WORKSPACE}', 
						projectUrl: '', 
						releaseNotes: '', 
						repositoryBranch: '', 
						repositoryCommit: '', 
						repositoryType: '', 
						repositoryUrl: '', 
						splitOutput: false, 
						traceLevel: 'None', 
						version: AutoVersion()
	        )
	            }
	        }
	

	         // Deploy Stages
	        stage('Deploy to Test') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to Test "

					UiPathDeploy (
					createProcess: true, 
					credentials:Token(accountName: '', credentialsId: '633cc25d-ef31-4240-aff6-86986d367266'), 
					entryPointPaths: 'Main.xaml', 
					environments: '', 
					folderName: 'Test', 
					ignoreLibraryDeployConflict: false, 
					orchestratorAddress: 'https://cloud.uipath.com/persortjnbdz',
					 orchestratorTenant: 'DefaultTenant', 
					 packagePath: "Output\\ ${env.BUILD_NUMBER'}", 
					 processName: 'CICD_test', 
					 processNames: '', 
					 traceLevel: 'None'


	

	        )
	            }
	        }

	    }
	

	}

