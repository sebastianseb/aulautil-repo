pipeline {
 agent any
 options {
   ansiColor('xterm')
   timestamps()
   timeout(time: 10, unit: 'MINUTES')
 }
  
 environment {
   ARTIFACT = "${env.BUILD_NUMBER}.zip"
   SLACK_MESSAGE = "Job ${env.JOB_NAME} Build ${env.BUILD_NUMBER} URL ${env.BUILD_URL}"
 }

   stages {
     stage('Repository') {
       steps {
          checkout scm
       }
     }
     stage('Test') {
       steps {
         parallel (
           sintaxis: { sh "echo check_syntax" },
           var_dump: { sh "echo grep_var_dump" }
         )
       }
     }

     stage('Build') {
       steps {
         sh "echo ${env.BUILD_NUMBER}"
         sh "echo ${env.WORKSPACE}"
         sh "touch archivo.txt"
         sh "mkdir -p micarpeta"
         sh "touch micarpeta/mifile.txt"
         sh "python main.py"
         script {
           def ID = sh(returnStdout: true, script: "./ami_id.sh ${env.BUILD_NUMBER}").trim()
           sh "./build_ami.sh ${ID}"
         }
         echo "${env.SLACK_MESSAGE}"
         echo "${params.SLACK_CHANNEL}"
         echo "${params.TYPE}"
         echo "${params.LC}"
        sh "zip -r ${env.ARTIFACT} ./"
       }
     }
     stage('Deploy') {
       when {
         expression {
           return params.LC ==~ /(?i)(Y|YES|T|TRUE|ON|RUN)/
         }
       }
       steps {
         sh "echo hagamos_deploy"
       }
     }
  }
 
post {
always {
archiveArtifacts artifacts: "${ARTIFACT}", onlyIfSuccessful: true
sh "rm -f ${ARTIFACT}"
echo "Job has finished"
 } 
 
}
def slackSendMessage(String color) {
 slackSend channel: "${params.SLACK_CHANNEL}",
 color: color,
 failOnError: true,
 message: "$SLACK_MESSAGE"
}
