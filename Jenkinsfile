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
   stage('PET') {
        //sh "ssh root@sith 'pgrep -f my-rest-services* | xargs kill -9'"
        sh "ssh root@sith '/opt/pet/kill.sh'"
        sh "ssh root@sith 'rm -f /opt/pet/my-rest-services-*.jar'"
        sh "scp target/*.jar root@sith:/opt/pet/"
        sh "ssh root@sith 'nohup java -jar /opt/pet/my-rest-services-*.jar >> /opt/pet/nohup.out 2>&1 &'"
        sleep 10
   }
   stage('QA') {
        sh 'newman run /var/lib/jenkins/workspace/my-rest-services/my-rest-services.postman_collection.json'
   }
   stage('Production') {
        echo 'Production'
   }
}