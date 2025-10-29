pipeline {

    agent any
 
    environment {

        BUILD_OUTPUT = "Target_folder"

    }
 
    stages {

        stage('Checkout') {

            steps {

                git branch: 'main',

                    credentialsId: 'ID',

                    url: 'Github_URL'

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

            echo 'Build completed successfully and files copied to Targetfolder'

        }

        failure {

            echo 'Build failed!'

        }

    }

}

 