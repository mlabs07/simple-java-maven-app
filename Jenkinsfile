node {

    def jarFilePath = 'target/my-app-1.0-SNAPSHOT.jar'
    def staticFolder = 'vercel-static'
    def vercelProjectName = 'dicoding-cicdjava-zulqifli'

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
                // sh """
                //     curl -X POST $apiUrl \
                //         -H "Authorization: Bearer $VERCEL_TOKEN" \
                //         -F "files[0]=@vercel-static/index.html" \
                //         -F "name=java-build-dicoding" \
                //         -F "target=production"
                // """
                

                sh """
                    echo "Mengunggah file ke Vercel..."

                    for f in ${staticFolder}/index.html ${staticFolder}/jar-files/*; do
                        fileName=\$(basename "\$f")
                        fileHash=\$(shasum -a 256 "\$f" | awk '{print \$1}')
                        
                        echo "Uploading: \$fileName with hash \$fileHash"

                        curl -X POST "https://api.vercel.com/v2/files" \
                            -H "Authorization: Bearer $VERCEL_TOKEN" \
                            -H "Content-Type: application/octet-stream" \
                            --data-binary "@\$f" \
                            -o response.json
                        
                        fileUrl=\$(jq -r '.url' response.json)
                        echo "{ \"file\": \"\$fileName\", \"sha\": \"\$fileHash\" }," >> fileList.json
                    done
                """

                
                sh """
                    echo "{ \"name\": \"${vercelProjectName}\", \"files\": [" > deploy.json
                    cat fileList.json >> deploy.json
                    echo "{} ] }" >> deploy.json

                    curl -X POST "https://api.vercel.com/v13/deployments" \
                        -H "Authorization: Bearer $VERCEL_TOKEN" \
                        -H "Content-Type: application/json" \
                        --data-binary "@deploy.json"
                """
            }
            sleep 60
            echo 'Deploy success'
        }
    }
}
