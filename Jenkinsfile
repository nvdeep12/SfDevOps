#!groovy

import groovy.json.JsonSlurperClassic

node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME
    
   // def build = this.getProperty('binding').getVariable('build')
  //  def listener = this.getProperty('binding').getVariable('listener')
   // def env = build.getEnvironment(listener)
    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH
    def JWT_KEY_FILE=env.JWT_KEY_FILE_PATH
   // def CONNECTEDAPPCONSUMERKEY_DH=env.CONNECTEDAPPCONSUMERKEYDH
    
    println 'JWT_KEY_FILE Path ==> '+JWT_KEY_FILE
    println 'KEY IS'
    println 'env value ==> '+env
    println 'env.HUB_ORG_DH ==> '+env.HUB_ORG_DH
    println 'JWT_KEY_CRED_ID ==> '+JWT_KEY_CRED_ID
    println 'HUB_ORG ==> '+HUB_ORG
    println 'SFDC_HOST ==> '+SFDC_HOST
    println 'CONNECTED_APP_CONSUMER_KEY ==> '+CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'
    // def toolbelt = env.toolbelt
    println 'toolbelt ==> '+toolbelt

    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------        
        
        withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
            stage('Deploye Code') {
              if(isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${JWT_KEY_FILE} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
               }
              else {
                  bat "${toolbelt} update" 
                  rc = bat returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${JWT_KEY_FILE} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
               }
              if(rc != 0) { error 'hub org authorization failed' }
              
              println rc
              
              // need to pull out assigned username
              if(isUnix()) {
                rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/ -u ${HUB_ORG}"
              } else {
                rmsg = bat returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/ -u ${HUB_ORG}"
              }
              
                printf rmsg
                println('Hello from a Job DSL script!')
                println(rmsg)
              }
        }
}
