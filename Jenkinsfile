pipeline {
    agent any

    environment {
        BUILD_OUTPUT = "job build"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'gitrepo', url: 'https://github.com/swetha-200160/push--it-easily.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building project...'
                bat "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\job build"
              
            }
        }

        stage('Copy Build Files') {
            steps {
                bat """
                    if not exist "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\job build" mkdir "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\job build"
                    copy /Y target\\*.jar "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\job build"
                    copy /Y target\\*.war "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\job build"
                """
            }
        }
    }

    post {
        success {
            echo '✅ Build completed successfully!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
