node {
    def dockerImageName = "Moin2811/javademoapp_${env.JOB_NAME}:${env.BUILD_NUMBER}"
    def mvnHome = tool name: 'Maven 3.5.4', type: 'maven'  // ✅ This will fetch Maven from Jenkins tool config

    stage('SCM Checkout') {
        git 'https://github.com/Moin2811/java-groovy-docker'
    }

    stage('Build') {
        sh "${mvnHome}/bin/mvn package -Dmaven.test.skip=true"
    }

    stage('Test') {
        sh "${mvnHome}/bin/mvn verify; sleep 3"
    }

    stage('Build Docker Image') {
        sh "docker build -t ${dockerImageName} ."
    }

    stage('Publish Docker Image') {
        withCredentials([string(credentialsId: 'dockerpwd', variable: 'dockerPWD')]) {
            sh "docker login -u Moin2811 -p ${dockerPWD}"
        }
        sh "docker push ${dockerImageName}"
    }

    stage('Run Docker Image') {
        def dockerContainerName = "javademoapp_${env.JOB_NAME}_${env.BUILD_NUMBER}"
        sh 'chmod +x stopscript.sh'
        sh './stopscript.sh'
        sh "docker run -p 8089:8090 -d --name ${dockerContainerName} ${dockerImageName}"
    }
}
