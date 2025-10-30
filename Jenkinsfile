pipeline {
  agent any

  environment {
    // Absolute destination on the Jenkins agent machine where files will be copied.
    // Change this path if you want files somewhere else.
    BUILD_OUTPUT = 'C:\\ProgramData\\Jenkins\\test_job'
    // Optional: set to a Maven installation root if you want to force a specific mvn.
    MAVEN_HOME = ''
  }

  stages {
    stage('Checkout') {
      steps {
        // clone the branch configured by the job / multibranch pipeline
        git branch: 'main',
            credentialsId: 'gitrepo',
            url: 'https://github.com/swetha-200160/push--it-easily.git'
      }
    }

    stage('Verify environment') {
      steps {
        // helpful diagnostics to understand what's available on the agent
        bat '''
        echo ==== WORKSPACE ====
        echo %CD%
        echo ==== BUILD_OUTPUT ====
        echo %BUILD_OUTPUT%
        echo ==== MAVEN_HOME ====
        echo %MAVEN_HOME%
        if exist mvnw.cmd ( echo mvnw.cmd FOUND ) else ( echo mvnw.cmd NOT FOUND )
        where mvn || echo mvn not found on PATH
        '''
      }
    }

    stage('Build') {
      steps {
        echo 'Building Java project (mvnw -> MAVEN_HOME -> mvn on PATH)'
        bat """
        REM 1) Try Maven wrapper in repo first (recommended)
        if exist mvnw.cmd (
          echo Running mvnw.cmd...
          mvnw.cmd -B -DskipTests clean package
          if %ERRORLEVEL% neq 0 exit /b %ERRORLEVEL%
        )

        REM 2) Try a specified MAVEN_HOME if provided
        if not "%MAVEN_HOME%"=="" (
          if exist "%MAVEN_HOME%\\bin\\mvn.cmd" (
            echo Running mvn from MAVEN_HOME...
            "%MAVEN_HOME%\\bin\\mvn.cmd" -B -DskipTests clean package
            if %ERRORLEVEL% neq 0 exit /b %ERRORLEVEL%
          ) else (
            echo MAVEN_HOME set but "%MAVEN_HOME%\\bin\\mvn.cmd" not found
          )
        )

        REM 3) Fallback to mvn on PATH
        echo Trying mvn from PATH...
        mvn -B -DskipTests clean package
        if %ERRORLEVEL% neq 0 (
          echo WARNING: mvn not found or build failed. Build may not produce artifacts.
          REM do not exit here — we'll still copy source files below
        )
        """
      }
    }

    stage('Copy Build Files and Source') {
      steps {
        script {
          // Create the destination directory if it does not exist
          bat 'if not exist "%BUILD_OUTPUT%" mkdir "%BUILD_OUTPUT%"'

          // Copy build artifacts (jar/war) from top-level target and submodule target directories
          bat """
          echo === Copying top-level artifacts if present ===
          copy /Y target\\*.jar "%BUILD_OUTPUT%\\" 2>nul || echo "No top-level JARs found"
          copy /Y target\\*.war "%BUILD_OUTPUT%\\" 2>nul || echo "No top-level WARs found"

          echo === Copying artifacts from submodule target folders (if any) ===
          for /F "delims=" %%D in ('dir /S /B /AD target 2^>nul') do (
            if exist "%%D\\*.jar" copy /Y "%%D\\*.jar" "%BUILD_OUTPUT%\\" >nul
            if exist "%%D\\*.war" copy /Y "%%D\\*.war" "%BUILD_OUTPUT%\\" >nul
          )
          """

          // Copy the entire repo source (your VS Code files) into a subfolder named 'source_<buildnumber>'
          // This avoids overwriting artifacts and keeps source organized per build.
          bat """
          set SRC_DEST=%BUILD_OUTPUT%\\source_%BUILD_NUMBER%
          if not exist "%SRC_DEST%" mkdir "%SRC_DEST%"
          echo Copying workspace (source files) to %SRC_DEST% (excluding .git)
          REM Copy all files and folders except .git directory
          xcopy /E /I /Y /EXCLUDE:.gitignore . "%SRC_DEST%\\" >nul || (
            echo xcopy failed or nothing to copy with xcopy; try robocopy...
            REM try robocopy if available (more robust)
            robocopy . "%SRC_DEST%" /MIR /XD .git /XF .gitignore >nul || echo "robocopy also failed"
          )
          echo === Destination listing (%BUILD_OUTPUT%) ===
          dir "%BUILD_OUTPUT%"
          """

          // Archive artifacts in Jenkins (optional) - archives anything we copied under BUILD_OUTPUT
          // Use forward slash path for Jenkins archiveArtifacts
          archiveArtifacts artifacts: "${env.BUILD_OUTPUT.replaceAll('\\\\','/')}/**/*", allowEmptyArchive: true
        }
      }
    }
  }

  post {
    success {
      echo "Build finished. Artifacts and source copied to ${env.BUILD_OUTPUT}"
    }
    failure {
      echo "Build failed (check console output). Source files were still copied to ${env.BUILD_OUTPUT} if checkout succeeded."
    }
    always {
      echo "Pipeline finished."
    }
  }
}
