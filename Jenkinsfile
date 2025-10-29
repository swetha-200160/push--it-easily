stage('Build') {
    steps {
        echo 'Building project...'
        bat '''
        cd "C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\job build"
        mvnw clean package -DskipTests
        '''
    }
}
