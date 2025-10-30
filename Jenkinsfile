pipeline {
  agent any

  // Ensure Jenkins provides Maven (must match the name in Global Tool Configuration)
  tools {
    maven 'M3'
    // optional: jdk 'JDK11'  // if you also configured JDK in Global Tool Config
  }

  environment {
    BUILD_OUTPUT = 'C:\\ProgramData\\Jenkins\\test_job'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main',
            credentialsId: 'gitrepo',
            url: 'https://github.com/swetha-200160/push--it-easily.git'
      }
    }

    stage('Verify tools') {
      steps {
        // Print path and verify mvn is available
        bat """
        echo ==== PATH ====
        echo %PATH%
        echo ==== MAVEN ====
        mvn -v
        """
      }
    }

    stage('Build') {
      steps {
        echo 'Building Java project...'
        // use Maven in non-interactive mode
        bat 'mvn -B -DskipTests clean package'
      }
    }

    stage('Copy Build Files') {
      steps {
        script {
          echo "Copying build files to ${env.test_job}"
          bat """
          if not exist "%test_job%" mkdir "%test_job%"
          copy /Y target\\*.jar "%test_job%\\"
          copy /Y target\\*.war "%test_job%\\"
          """
        }
      }
    }
  }

  post {
    success {
      echo "Build completed successfully and files copied to ${env.test_job}"
    }
    failure {
      echo 'Build failed!'
    }
  }
}
