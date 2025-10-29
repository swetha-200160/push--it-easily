pipeline {
    agent any

    environment {
        BUILD_OUTPUT = "build files"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'gitrepo/******', url: 'https://github.com/swetha-200160/push--it-easily.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building project...'
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Copy Build Files') {
            steps {
                bat """
                    if not exist "C:\build files" mkdir "C:\build files"
                    copy /Y target\\*.jar "C:\build files\\"
                    copy /Y target\\*.war "C:\build files\\"
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
