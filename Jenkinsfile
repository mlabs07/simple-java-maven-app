node {

    def jarFilePath = 'target/my-app-1.0-SNAPSHOT.jar'

    docker.image('maven:3.9.0').inside("--network host") {
        // build
        stage('Build') {
            checkout scm
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
            sh "cp ${jarFilePath} vercel-static/jar-files/"

            withCredentials([string(credentialsId: 'vercel_token', variable: 'VERCEL_TOKEN')]) {
                // sh 'vercel --token=$VERCEL_TOKEN --prod --yes'
                def apiUrl = "https://api.vercel.com/v12/now/deployments"
                sh """
                    curl -X POST $apiUrl \
                        -H "Authorization: Bearer $VERCEL_TOKEN" \
                        -F "files[]=@vercel-static/index.html" \
                        -F "files[]=@vercel-static/jar-files/*" \
                        -F "name=java-build-dicoding" \
                        -F "target=production"
                """
            }
            sleep 60
            echo 'Deploy success'
        }
    }
}
