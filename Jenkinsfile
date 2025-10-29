pipeline {
  agent any
  environment { GIT_CRED='gitrepo'; BRANCH='main'; DEPLOY_DIR='C:\\inetpub\\wwwroot\\myapp' }
  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/swetha-200160/push--it-easily.git', credentialsId: env.GIT_CRED, branch: env.BRANCH
      }
    }
    stage('Prepare build') {
      steps {
        powershell '''
        if (Test-Path build) { Remove-Item -Recurse -Force build }
        New-Item -ItemType Directory -Path build | Out-Null
        Get-ChildItem -Path . -Include *.html,*.htm,*.css,*.js -Recurse | ForEach-Object {
          $rel = $_.FullName.Substring((Get-Location).Path.Length).TrimStart('\')
          $dest = Join-Path (Join-Path (Get-Location) 'build') $rel
          $dird = Split-Path $dest -Parent
          if (-not (Test-Path $dird)) { New-Item -ItemType Directory -Path $dird | Out-Null }
          Copy-Item -LiteralPath $_.FullName -Destination $dest -Force
        }
        '''
      }
    }
    stage('Package') {
      steps {
        powershell 'Compress-Archive -Path build\\* -DestinationPath build.zip -Force'
        archiveArtifacts artifacts: 'build.zip', fingerprint: true
      }
    }
    stage('Deploy') {
      when { expression { return env.DEPLOY_DIR && env.DEPLOY_DIR.trim() != '' } }
      steps { powershell "if (-not (Test-Path '${env.DEPLOY_DIR}')) { New-Item -ItemType Directory -Path '${env.DEPLOY_DIR}' | Out-Null } ; robocopy build '${env.DEPLOY_DIR}' /MIR /NFL /NDL /NJH /NJS" }
    }
  }
}