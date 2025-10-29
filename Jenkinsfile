pipeline {
    agent any

    environment {
        BUILD_OUTPUT = "Target_folder"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: 'ID', url: 'https://github.com/yourname/yourrepo.git'
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
                    if not exist "${env.BUILD_OUTPUT}" mkdir "${env.BUILD_OUTPUT}"
                    copy /Y target\\*.jar "${env.BUILD_OUTPUT}\\"
                    copy /Y target\\*.war "${env.BUILD_OUTPUT}\\"
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
