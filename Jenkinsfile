pipeline {
    agent any

    environment {
        BUILD_OUTPUT = "job_build"  // ✅ Avoid spaces in folder names
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
                echo 'Building Java project...'
                // ✅ Use Maven (make sure Maven is installed & on PATH)
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Copy Build Files') {
            steps {
                script {
                    echo "Copying build files to ${env.BUILD_OUTPUT}"

                    // ✅ Safe Windows batch commands — no spaces, no special chars
                    bat """
                    if not exist "${env.BUILD_OUTPUT}" mkdir "${env.BUILD_OUTPUT}"
                    copy /Y target\\*.jar "${env.BUILD_OUTPUT}\\"
                    copy /Y target\\*.war "${env.BUILD_OUTPUT}\\"
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build completed successfully and files copied to job_build folder'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
