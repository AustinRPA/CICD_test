pipeline {
  agent any

  environment {
    MAJOR = '1'
    MINOR = '0'

    // Keep this relative; we'll prepend WORKSPACE at use time
    UIPATH_PACKAGE_REL = "Output/${BUILD_NUMBER}"

    // Orchestrator (Cloud)
    UIPATH_ORCH_URL_BASE = "https://cloud.uipath.com"
    UIPATH_ORCH_TENANT = "DefaultTenant"         // or tenantLogicalName, see below
    UIPATH_ORCH_FOLDER = "Test"
    // Your Org (account) logical name for Cloud tokens
    UIPATH_ACCOUNT_LOGICAL_NAME = "persortjnbdz"
  }

  stages {

    stage('Preparing') {
      steps {
        echo "Jenkins Home ${env.JENKINS_HOME}"
        echo "Jenkins URL ${env.JENKINS_URL}"
        echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
        echo "Jenkins JOB Name ${env.JOB_NAME}"
        echo "GitHub BranchName ${env.BRANCH_NAME}"
        checkout scm
      }
    }

    stage('Build') {
      steps {
        echo "Building..with ${env.WORKSPACE}"
        UiPathPack(
          disableBuiltInNugetFeeds: false,
          governanceFilePath: '',
          outputPath: "Output/${env.BUILD_NUMBER}",
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
          version: "${env.MAJOR}.${env.MINOR}.${env.BUILD_NUMBER}"
        )
      }
    }

    stage('Deploy to Test') {
      steps {
        script {
          // Build the absolute output folder path safely
          def outDir = "${env.WORKSPACE}/${env.UIPATH_PACKAGE_REL}"

          // IMPORTANT: set this to your actual project name from project.json -> "name"
          def pkgProjectName = "CICD_test"

          def pkgPath = "${outDir}/${pkgProjectName}.${env.MAJOR}.${env.MINOR}.${env.BUILD_NUMBER}.nupkg"
          echo "Deploying ${env.BRANCH_NAME} using package: ${pkgPath}"

          // If your plugin version uses tenantLogicalName/accountLogicalName, prefer those fields.
          // If it uses orchestratorTenant, keep that and omit tenantLogicalName.
          UiPathDeploy(
            createProcess: true,
            credentials: Token(
              accountName: "${env.UIPATH_ACCOUNT_LOGICAL_NAME}",      // <-- must not be empty
              credentialsId: '633cc25d-ef31-4240-aff6-86986d367266'   // <-- your Jenkins secret id
            ),
            entryPointPaths: 'Main.xaml',
            environments: '',
            folderName: "${env.UIPATH_ORCH_FOLDER}",
            ignoreLibraryDeployConflict: false,

            orchestratorAddress: "${env.UIPATH_ORCH_URL_BASE}",


            orchestratorTenant: "${env.UIPATH_ORCH_TENANT}",



            packagePath: pkgPath,
            processName: "${pkgProjectName}",
            processNames: '',
            traceLevel: 'None'
          )
        }
      }
    }
  }
}