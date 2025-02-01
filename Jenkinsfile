node {

    def jarFilePath = 'target/my-app-1.0-SNAPSHOT.jar'

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
            sh 'mkdir -p vercel-static/static'
            sh 'cp ${jarFilePath} vercel-static/static/'

            withCredentials([string(credentialsId: 'vercel_token', variable: 'VERCEL_TOKEN')]) {
                // sh 'vercel --token=$VERCEL_TOKEN --prod --yes'
                def apiUrl = "https://api.vercel.com/v12/now/deployments"
                sh """
                    curl -X POST $apiUrl \
                        -H "Authorization: Bearer $VERCEL_TOKEN" \
                        -F "file=@vercel-static/static/\$(basename $jarFilePath)" \
                        -F "name=vercel-static" \
                        -F "files[0]=vercel-static/static/\$(basename $jarFilePath)"
                """
            }
            sleep 60
            echo 'Deploy success'
        }
    }
}
