node {
    stage('Checkout') {
        git 'https://github.com/warawicht/my-rest-services.git'
    }
    stage('Build') {
        try {
            sh "mvn -U clean package -Dbuild.number=${BUILD_NUMBER}"
        } catch(err) {
            step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
            if (currentBuild.result == 'UNSTABLE') {
                currentBuild.result = 'FAILURE'
            }
            throw err
        }
   }
    stage('Reports') {
        junit '**/target/surefire-reports/TEST-*.xml'
   }
   stage('Archive') {
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
   }
   stage('Alpha') {
        sh "/opt/alpha/kill.sh"
        sh 'rm /opt/alpha/*.jar'
        sh 'cp target/*.jar /opt/alpha/'
        withEnv(['BUILD_ID=dontKillMe']) {
            //sh '/opt/alpha/start.sh &'
            sh "BUILD_ID=dontKillMe nohup java -Dserver.port=9001 -jar /opt/alpha/my-rest-services-*.jar >> /opt/alpha/nohup.out 2>&1 &"
        }
   }
   stage('QA') {
        echo 'QA'
   }
   stage('Production') {
        echo 'Production'
   }
}