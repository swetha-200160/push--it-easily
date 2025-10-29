pipeline {
    agent any

    environment {
        BUILD_OUTPUT = "C:\\build_output"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'gitrepo',
                    url: 'https://github.com/swetha-200160/push--it-easily.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Running build simulation...'
                bat 'echo Build successful > output.txt'
            }
        }

        stage('Copy Build Files') {
            steps {
                bat """
                if not exist "${env.BUILD_OUTPUT}" mkdir "${env.BUILD_OUTPUT}"
                copy /Y output.txt "${env.BUILD_OUTPUT}\\"
                """
            }
        }
    }

    post {
        success {
            echo '✅ Build finished and files copied successfully!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
