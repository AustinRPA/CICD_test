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
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
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
						outputPath: '${WORKSPACE}\\Output', 
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
					credentials: ExternalApp(accountForApp: '', applicationId: 'b2ed53a6-21bb-4ca4-a273-a8926d712c41', applicationScope: 'OR.Administration OR.Administration.Read OR.Administration.Write OR.Analytics OR.Analytics.Read OR.Analytics.Write OR.Assets OR.Assets.Read OR.Assets.Write OR.Audit OR.Audit.Read OR.Audit.Write OR.AutomationSolutions.Access OR.BackgroundTasks OR.BackgroundTasks.Read OR.BackgroundTasks.Write OR.Execution OR.Execution.Read OR.Execution.Write OR.Folders OR.Folders.Read OR.Folders.Write OR.Hypervisor OR.Hypervisor.Read OR.Hypervisor.Write OR.Jobs OR.Jobs.Read OR.Jobs.Write OR.License OR.License.Read OR.License.Write OR.Machines OR.Machines.Read OR.Machines.Write OR.ML OR.ML.Read OR.ML.Write OR.Monitoring OR.Monitoring.Read OR.Monitoring.Write OR.Queues OR.Queues.Read OR.Queues.Write OR.Robots OR.Robots.Read OR.Robots.Write OR.Settings OR.Settings.Read OR.Settings.Write OR.Tasks OR.Tasks.Read OR.Tasks.Write OR.TestDataQueues OR.TestDataQueues.Read OR.TestDataQueues.Write OR.TestSetExecutions OR.TestSetExecutions.Read OR.TestSetExecutions.Write OR.TestSets OR.TestSets.Read OR.TestSets.Write OR.TestSetSchedules OR.TestSetSchedules.Read OR.TestSetSchedules.Write OR.Users OR.Users.Read OR.Users.Write OR.Webhooks OR.Webhooks.Read OR.Webhooks.Write', 
					applicationSecret: '2', 
					identityUrl: ''), 
					entryPointPaths: 'Main.xaml', 
					environments: '', 
					folderName: 'Test', 
					ignoreLibraryDeployConflict: false, 
					orchestratorAddress: 'https://cloud.uipath.com/persortjnbdz',
					 orchestratorTenant: 'DefaultTenant', 
					 packagePath: 'Output\\${env.BUILD_NUMBER}', 
					 processName: 'CICD_Test', 
					 processNames: '', 
					 traceLevel: 'None'


	

	        )
	            }
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
}
