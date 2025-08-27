pipeline {
  agent any

  environment {
    // --- REQUIRED: set these to your values ---
    PROJECT_NAME              = 'CICD_test'               // Must match project.json -> "name"
    CREDENTIALS_ID            = '633cc25d-ef31-4240-aff6-86986d367266' // Jenkins secret id for UiPath token
    ACCOUNT_LOGICAL_NAME      = 'persortjnbdz'            // UiPath Cloud organization (account) logical name
    ORCHESTRATOR_TENANT       = 'DefaultTenant'           // UiPath tenant (or tenantLogicalName if your plugin uses that)
    ORCHESTRATOR_FOLDER       = 'Test'                    // Target folder
    ORCHESTRATOR_BASE_URL     = 'https://cloud.uipath.com'

    // Output dir will be e.g. Output/123 for BUILD_NUMBER=123
    UIPATH_OUTPUT_REL         = "Output/${BUILD_NUMBER}"
  }

  stages {

    stage('Prepare') {
      steps {
        echo "Jenkins Home: ${env.JENKINS_HOME}"
        echo "Build #: ${env.BUILD_NUMBER}"
        echo "Job: ${env.JOB_NAME}"
        echo "Branch: ${env.BRANCH_NAME}"
        checkout scm
      }
    }

    stage('Pack (Auto Version)') {
      steps {
        echo "Packing project with AutoVersion into ${env.UIPATH_OUTPUT_REL}"
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

          // IMPORTANT: Auto-generate version (SelectEntry type)
          version: [$class: 'AutoVersionEntry']
        )
      }
    }

    stage('Deploy to Test') {
      steps {
        script {
          // Build absolute output path
          def outDir = "${env.WORKSPACE}/${env.UIPATH_OUTPUT_REL}"

          // Find the generated .nupkg file (Auto version -> unknown name suffix at author time)
          String pkgPath
          if (isUnix()) {
            pkgPath = sh(
              script: "ls -1 \"${outDir}\"/*.nupkg | head -n 1",
              returnStdout: true
            ).trim()
          } else {
            // Use PowerShell on Windows to get the first .nupkg full path
            pkgPath = powershell(
              returnStdout: true,
              script: """
                \$f = Get-ChildItem -Path '${outDir}' -Filter *.nupkg | Select-Object -First 1 -ExpandProperty FullName
                if (-not \$f) { exit 2 } else { Write-Output \$f }
              """
            ).trim()
          }

          if (!pkgPath) {
            error "No .nupkg found in ${outDir}. Check the Pack stage output."
          }

          echo "Deploying package: ${pkgPath}"

          UiPathDeploy(
            createProcess: true,
            credentials: Token(
              accountName: "${env.ACCOUNT_LOGICAL_NAME}",
              credentialsId: "${env.CREDENTIALS_ID}"
            ),

            // Use lists for multi-select fields
            entryPointPaths: ['Main.xaml'],
            environments: [],                  // or e.g. ['Test']
            processNames: [],                  // keep empty unless updating specific existing processes

            folderName: "${env.ORCHESTRATOR_FOLDER}",
            ignoreLibraryDeployConflict: false,

            // Cloud base URL + tenant (keep this combo if your plugin expects orchestratorTenant)
            orchestratorAddress: "${env.ORCHESTRATOR_BASE_URL}",
            orchestratorTenant: "${env.ORCHESTRATOR_TENANT}",

            packagePath: pkgPath,
            processName: "${env.PROJECT_NAME}",
            traceLevel: 'None'
          )
        }
      }
    }
  }
}