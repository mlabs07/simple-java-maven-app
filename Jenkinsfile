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
        // manual approval
        stage('Manual Approval'){
            input message: 'Lanjut ke tahap Deploy? (Klik "Proceed untuk lanjutkan")'
        }
        // 
        stage('deploy') {
            sleep 60
            echo 'Deploy success'
        }
    }
}
