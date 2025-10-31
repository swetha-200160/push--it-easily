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
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Copy Build Files (all workspace)') {
            steps {
                script {
                    echo "Copying entire workspace to ${env.BUILD_OUTPUT} (preserving folders)"
                    bat """
                    REM ensure destination exists
                    if not exist "${env.BUILD_OUTPUT}" mkdir "${env.BUILD_OUTPUT}"

                    REM use robocopy to copy current workspace to destination, exclude .git
                    REM /E = copy subdirectories including empty ones
                    REM /XD ".git" = exclude .git directory
                    REM /NFL /NDL = reduce logging lines (optional)
                    robocopy "%CD%" "${env.BUILD_OUTPUT}" /E /XO /R:2 /W:2 /XD ".git" /NFL /NDL

                    REM robocopy returns 0-7 for success-ish states; >7 indicates failure
                    set RC=%ERRORLEVEL%
                    echo Robocopy exit code: %RC%

                    if %RC% LEQ 7 (
                        echo Robocopy completed successfully (or with acceptable return code %RC%).
                        exit /b 0
                    ) else (
                        echo Robocopy failed with return code %RC%.
                        exit /b %RC%
                    )
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully and entire workspace copied to target folder'
        }
        failure {
            echo 'Build or copy failed!'
        }
    }
}
