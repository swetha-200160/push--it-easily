pipeline {

    agent any
 
    environment {

        BUILD_OUTPUT = "push--it-easily"

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

                    echo "Copying build files to ${env.push--it-easily}"

                    // Adjust target path based on project structure (e.g., target/*.jar)

                    bat """

                    if not exist "${env.test_job}" mkdir "${env.test_job}"

                    copy /Y target\\*.jar "${env.test_job}\\"

                    copy /Y target\\*.war "${env.test_job}\\"

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

 