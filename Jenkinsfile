#!/usr/bin/env groovy

pipeline {
    agent any
    environment {
        BRANCH_PATTERN= '*'
    }
        parameters {
            choice(
                name: 'BUILD_TYPE',
                choices:"nightly\nsnapshots\nrc",
                description: "Build Type")
            booleanParam(name: 'FULL_BUILD', defaultValue: false)
            booleanParam(name: 'PATCH_BUILD', defaultValue: true)
            choice(name: 'WLS_VERSION', choices: ['12.2.1.2.0', '12.1.3'], description: 'WLS Client Version')
    }
    
    options {
    // Only keep the 10 most recent builds
    buildDiscarder(logRotator(numToKeepStr:'10'))
  }
    stages {
        stage("build") {
            steps {
                echo "Hello ${params.BUILD_TYPE}"
                echo "Global ${SE_DEFAULT_BRANCH}"
            }
        }
        
        stage("Send Success Mail"){
                    emailext body: '${FILE, path="build-mail.template"}',
                    mimeType: 'text/html',
                    subject: '$PROJECT_NAME - Build # $FS_BUILD_NUM $BUILD_TYPE - $BUILD_STATUS!',
                    to: '${env.FS_CORE_BUILD_RECIPIENTS}'    
                    }

                stage('Git Push To Origin') {
                    when {
                          // Nightly or snapshot build is selected
                            expression { params.BUILD_TYPE != 'snapshots' }
                   }
        steps {
                   
                   withCredentials([sshUserPrivateKey(credentialsId: 'sshgit', keyFileVariable: 'SSH_KEY')]) {
                   sh 'git tag -a v0.30 -m "my version v0.30"'
                   sh "git push https://${Username}:'${Password}'@github.com/bmangesh/JenkinsDSL.git  refs/tags/v0.20"
                   sh "git push origin  refs/tags/v0.30"

              }
           
        }   
                }   
                  
     }
    post {
        always {
          step([$class: 'Mailer',
            notifyEveryUnstableBuild: true,
            recipients: "mangesh.bharsakle@afourtech.com,mangesh.bharsakle@fixstream.com",
            sendToIndividuals: true])
        }   
     
      }
} 
