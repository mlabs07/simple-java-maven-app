node {
    docker.image('maven:3.9.0').inside("--network host") {
        // build
        stage('Build') {
            sh 'mvn -B -DskipTests clean package'
        }
        
        // test
        stage('Test') {
            sh 'mvn test'
            
            stage('Post-Test') {
                junit 'target/surefire-reports/*.xml'
            }
        }
    }
}
