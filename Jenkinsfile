pipeline {
    agent any

    environment {
        BUILD_OUTPUT = "C:\\ProgramData\\Jenkins\\test_job"
    }

    tools {
        // Optional: Jenkins tool name for Maven. Create a Maven installation in Jenkins Global Tool Configuration named 'M3'
        // If you don't have it, the pipeline will attempt mvnw first and then fail with a clear message.
        maven 'M3'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'gitrepo',
                    url: 'https://github.com/swetha-200160/push--it-easily.git'
            }
        }

        stage('Prepare') {
            steps {
                echo "Workspace: ${env.WORKSPACE}"
                // show mvn if available
                bat '''
                echo ---------- check mvn ----------
                where mvn || echo "mvn not found on PATH"
                echo ---------- end check ----------
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Building Java project...'
                script {
                    // If repo includes Maven wrapper, prefer it
                    if (fileExists('mvnw') || fileExists('mvnw.cmd')) {
                        echo "Found Maven Wrapper. Running mvnw..."
                        // Windows - use mvnw.cmd if present
                        if (isUnix()) {
                            sh './mvnw clean package -DskipTests'
                        } else {
                            // ensure executable flag for mvnw.cmd or mvnw (Windows)
                            if (fileExists('mvnw.cmd')) {
                                bat 'mvnw.cmd clean package -DskipTests'
                            } else {
                                bat '.\\mvnw clean package -DskipTests'
                            }
                        }
                    } else {
                        // Try Jenkins-configured Maven tool
                        try {
                            def mvnHome = tool name: 'M3', type: 'maven'
                            echo "Using Maven from Jenkins tool: ${mvnHome}"
                            if (isUnix()) {
                                sh "${mvnHome}/bin/mvn clean package -DskipTests"
                            } else {
                                bat "\"${mvnHome}\\bin\\mvn\" clean package -DskipTests"
                            }
                        } catch (err) {
                            error """
                            Maven wrapper not found and Jenkins Maven tool 'M3' is not configured or failed.
                            Options to fix:
                              1) Add the Maven Wrapper to your repo (recommended): run `mvn -N io.takari:maven:wrapper` locally and commit mvnw* files.
                              2) Install Maven on the Jenkins node and either add it to PATH or configure it in Jenkins Global Tool Configuration named 'M3'.
                            Build aborted.
                            """
                        }
                    }
                }
            }
        }

        stage('Copy Build Files') {
            when {
                expression {
                    // Only run copy if there is any artifact produced
                    return fileExists('target') && (fileExists('target/*.jar') || fileExists('target/*.war') || new File("${env.WORKSPACE}/target").list().length > 0)
                }
            }
            steps {
                script {
                    echo "Copying artifact(s) from target to ${env.BUILD_OUTPUT}"
                    // create dir if not exists and copy files
                    if (isUnix()) {
                        sh """
                        mkdir -p "${env.BUILD_OUTPUT}"
                        cp -v target/*.jar "${env.BUILD_OUTPUT}/" || true
                        cp -v target/*.war "${env.BUILD_OUTPUT}/" || true
                        """
                    } else {
                        bat """
                        if not exist "${env.BUILD_OUTPUT}" mkdir "${env.BUILD_OUTPUT}"
                        copy /Y target\\*.jar "${env.BUILD_OUTPUT}\\" || echo "No jar found"
                        copy /Y target\\*.war "${env.BUILD_OUTPUT}\\" || echo "No war found"
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build completed successfully."
            archiveArtifacts artifacts: 'target/*.jar, target/*.war', allowEmptyArchive: true
            echo "Artifacts archived (if present)."
        }
        failure {
            echo "Build failed!"
        }
        always {
            echo "Workspace: ${env.WORKSPACE}"
        }
    }
}
