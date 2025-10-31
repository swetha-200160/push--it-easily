pipeline {
    agent any
 
    environment {
        BUILD_OUTPUT = "C:/Users/swethasuresh/target"
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
                // If project uses Maven wrapper
                bat 'mvn clean package -DskipTests'
            }
        }
 
        stage('Copy Build Files') {
            steps {
                script {
                    echo "Copying build files to ${env.BUILD_OUTPUT}"
                    // Adjust target path based on project structure (e.g., target/*.jar)
                    bat """
                    if not exist "C:/Users/swethasuresh/target" mkdir "C:/Users/swethasuresh/target"          
                    copy /Y target\\*.jar  "C:/Users/swethasuresh/target\" 
                    copy /Y target\\*.war  "C:/Users/swethasuresh/target\" 
 
                    """
                }
            }
        }
    }
 
    post {
        success {
            echo 'Build completed successfully and files copied to Targetfolder'
        }
        failure {
            echo 'Build failed!'
        }
    }
}