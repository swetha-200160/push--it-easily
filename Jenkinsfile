pipeline {
    agent any

    environment {
        // Use backslashes for Windows paths and match what we use later
        BUILD_OUTPUT = "C:\\ProgramData\\Jenkins\\test_job"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Cloning repository..."
                git branch: 'main',
                    credentialsId: 'gitrepo',
                    url: 'https://github.com/swetha-200160/push--it-easily.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building Java project...'
                // Use mvnw if you added the Maven wrapper, otherwise make sure 'mvn' is available on PATH
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Copy Build Files') {
            steps {
                script {
                    echo "Copying build files to ${env.BUILD_OUTPUT}"
                    // Use the declared BUILD_OUTPUT env var (not env.test_job)
                    bat """
                    if not exist "${env.BUILD_OUTPUT}" mkdir "${env.BUILD_OUTPUT}"
                    copy /Y target\\*.jar "${env.BUILD_OUTPUT}" || echo No JAR files found
                    copy /Y target\\*.war "${env.BUILD_OUTPUT}" || echo No WAR files found
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Build completed successfully and files copied to ${env.BUILD_OUTPUT}"
        }
        failure {
            echo 'Build failed!'
        }
    }
}
