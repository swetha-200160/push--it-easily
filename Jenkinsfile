pipeline {

    agent any
 
    environment {

        BUILD_OUTPUT = "auto build"

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

                    echo "Copying build files to ${env.auto build}"

                    // Adjust target path based on project structure (e.g., target/*.jar)

                    bat """

                    if not exist "${env.auto build}" mkdir "${env.auto build}"

                    copy /Y target\\*.jar "C:\ProgramData\Jenkins\.jenkins\workspace\auto build"

                    copy /Y target\\*.war "C:\ProgramData\Jenkins\.jenkins\workspace\auto build"

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

 