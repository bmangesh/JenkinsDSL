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
                
                   
                withCredentials([sshUserPrivateKey(credentialsId: 'sshgit', keyFileVariable: 'SSH_KEY')]) {
                // sshagent (credentials: ['jenkins-generated-ssh-key']) {    
                    sh 'env' 
                  //  sh 'git tag -a v0.30 -m "my version v0.30"'
                   // sh "git push https://${Username}:'${Password}'@github.com/bmangesh/JenkinsDSL.git  refs/tags/v0.20"
                   // sh "git push origin  refs/tags/v0.30"

              }
           
        }   
                }   
        
        stage('send mail')
        {
            steps {
                      //  emailext body: 'test mail', subject: 'test Mail', to: 'mangesh.bharsakle@afourtech.com,cc:mangesh.bharsakle@fixstream.com'
            emailext body: '''Hi All,
<br/>
<br/>
Full Build:- <a href="http://172.16.2.22/nexus/content/repositories/${env.SE_DEFAULT_BRANCH}/com/fixstream/serviceeye/service-eye/${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}/service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}.tgz">service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}.tgz</a>
<br/>
<br/>
Patch Build:- <a href="http://172.16.2.22/nexus/content/repositories/env.${env.SE_DEFAULT_BRANCH}/com/fixstream/serviceeye/service-eye/${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}/service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-patch.tgz">service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-patch.tgz</a>
<br/>
<br/>
UI Tar:- <a href="http://172.16.2.22/nexus/content/repositories/${env.currentBuild.fullDisplayName}/com/fixstream/serviceeye/service-eye-ui/${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}/service-eye-ui-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}.tar.gz">service-eye-ui-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}.tar.gz</a>
<br/>
<br/>
Rest/Connector Jar:- <a href="http://172.16.2.22/nexus/content/repositories/${REPO_NAME}/com/fixstream/serviceeye/service-eye-core/${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}/service-eye-core-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}.jar">service-eye-core-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}.jar</a>
<br/>
<br/>
Logstash Base Zip:- <a href="http://172.16.2.22/nexus/content/repositories/${REPO_NAME}/com/fixstream/serviceeye/service-eye/${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}/service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-logstash-base.zip">service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-logstash-base.zip</a>
<br/>
<br/>
Logstash Correlation Zip:- <a href="http://172.16.2.22/nexus/content/repositories/${REPO_NAME}/com/fixstream/serviceeye/service-eye/${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}/service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-logstash-correlation.zip">service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-logstash-correlation.zip</a>
<br/>
<br/>
Logstash Ingestion Zip:- <a href="http://172.16.2.22/nexus/content/repositories/${REPO_NAME}/com/fixstream/serviceeye/service-eye/${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}/service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-logstash-ingestion.zip">service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-logstash-ingestion.zip</a>
<br/>
<br/>
Elasticsearch Zip:- <a href="http://172.16.2.22/nexus/content/repositories/${REPO_NAME}/com/fixstream/serviceeye/service-eye/${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}/service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-elasticsearch.zip">service-eye-${BRANCH_TO_BE_BUILT}.${SE_BUILD_NUM}-elasticsearch.zip</a>
<br/>
<br/>
Jenkins URL:- <a href="$BUILD_URL">$BUILD_URL</a>
<br/>
<br/>
Thanks,
<br/>
DevOps Team''', mimeType: '', subject: 'test Mail', to: 'mangesh.bharsakle@afourtech.com,cc:mangesh.bharsakle@fixstream.com'


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
