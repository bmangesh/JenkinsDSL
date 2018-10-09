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

                stage('Git Push To Origin') {

        steps {
                sh 'git tag v0.1'
                sshagent (credentials: ['sshgit']) {
                    sh 'git push --tags'
                   
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
        success {
            mail to:"mangesh.bharsakle@afourtech.com,mangesh.bharsakle@fixstream.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Yay, we passed."
        }
      }
} 
