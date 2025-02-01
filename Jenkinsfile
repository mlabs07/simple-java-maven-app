node {

    def jarFilePath = 'target/my-app-1.0-SNAPSHOT.jar'
    def staticFolder = 'vercel-static'
    def vercelProjectName = 'dicoding-cicdjava-zulqifli'

    docker.image('maven:3.9.0').inside("--network host --user root") {
        // build
        stage('Build') {
            checkout scm
            sh 'apt update && apt install -y jq'
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
                    echo "Mengunggah index.html ke Vercel..."
                    
                    filePath=${staticFolder}/index.html

                    # Upload file index.html ke Vercel
                    curl -X POST "https://api.vercel.com/v13/now/deployments" \
                        -H "Authorization: Bearer $VERCEL_TOKEN" \
                        -F "file=@\$filePath" \
                        -F "name=vercel-static" \
                        -F "target=production" \
                        -o response.json

                    # Ambil URL file yang sudah di-deploy dari response
                    fileUrl=\$(jq -r '.url' response.json)

                    echo "File berhasil diupload dan tersedia di: \$fileUrl"
                """
            }
            sleep 60
            echo 'Deploy success'
        }
    }
}
