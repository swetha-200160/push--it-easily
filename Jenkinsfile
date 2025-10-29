pipeline {
    agent any

    environment {
        // This creates a build folder dynamically inside Jenkins workspace
        BUILD_OUTPUT = "${env.WORKSPACE}\\build_output"
    }

    stages {

        stage('Checkout') {
            steps {
                // Clone your GitHub repo
                git branch: 'main',
                    credentialsId: 'gitrepo',
                    url: 'https://github.com/swetha-200160/push--it-easily.git'
            }
        }

        stage('Build') {
            steps {
                echo '🔧 Building Java project...'
                // Run Maven to create build files
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Prepare Build Folder') {
            steps {
                echo "📁 Creating build folder at: ${env.BUILD_OUTPUT}"
                bat """
                if not exist "${env.BUILD_OUTPUT}" mkdir "${env.BUILD_OUTPUT}"
                """
            }
        }

        stage('Copy Build Files') {
            steps {
                echo "📦 Copying build files to ${env.BUILD_OUTPUT}"
                bat """
                copy /Y target\\*.jar "${env.BUILD_OUTPUT}\\"
                copy /Y target\\*.war "${env.BUILD_OUTPUT}\\"
                """
            }
        }

        stage('Show Folder Path') {
            steps {
                echo "✅ Build files are available at: ${env.BUILD_OUTPUT}"
            }
        }
    }

    post {
        success {
            echo '✅ Build completed successfully and files copied to build_output folder!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}
