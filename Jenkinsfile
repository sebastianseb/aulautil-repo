pipeline {
 agent any
 options {
   ansiColor('xterm')
   timestamps()
   timeout(time: 10, unit: 'MINUTES')
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
         
         echo "${env.SLACK_MESSAGE}"
         echo "${params.SLACK_CHANNEL}"
         echo "${params.TYPE}"
         echo "${params.LC}"
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
}

