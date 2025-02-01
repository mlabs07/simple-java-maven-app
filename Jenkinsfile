node {

    def jarFilePath = 'target/my-app-1.0-SNAPSHOT.jar'
    def staticFolder = 'vercel-static'
    def projectName = 'dicoding-cicdjava-zulqifli'

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
    }

    docker.image('node:18-buster-slim').inside("-p 3000:3000 --user root") {
        // deploy
        stage('Deploy') {
            sh "cp ${jarFilePath} vercel-static/jar-files/"

            withCredentials([string(credentialsId: 'vercel_token', variable: 'VERCEL_TOKEN')]) {
                sh 'vercel --token=$VERCEL_TOKEN --cwd $staticFolder --name $projectName --prod --yes'
            }

            sleep 60
            echo 'Deploy success'
        }
    }
}
