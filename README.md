
    
@Library('jenkins-shared-scripts@feature') _
import groovy.json.JsonSlurper
pipeline {​​​​​​​
    agent {​​​​​​​
        label "mesos"
    }​​​​​​​


    environment {​​​​​​​
        ANYPOINT_CREDENTIALS = credentials('anypoint.credentials.nonprod')
        ANYPOINT_ENV_ID = ""
        ANYPOINT_ORG_ID = ""
        MAVEN_HOME = "/opt/maven/maven-3.6.1/bin/"
        BUILD_VERSION = "test"
        ASSET_VERSION = "v1"
        token = getAnypointToken(username: "${​​​​​​​ANYPOINT_CREDENTIALS_USR}​​​​​​​", password: "${​​​​​​​ANYPOINT_CREDENTIALS_PSW}​​​​​​​")
    }​​​​​​​


    tools {​​​​​​​
        maven 'Maven3.6.1'
        jdk 'ORACLEJDK8'
    }​​​​​​​


    options {​​​​​​​
        //skips default checkout from Git whenever you enter a new stage with a new build agent.  DO NOT CHANGE
        skipDefaultCheckout()
    }​​​​​​​


    stages {​​​​​​​
        stage('Parse YAML') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    println('*** Parse YAML Step ***')
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​



    stage('Pull ArcheType') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    println('*** Pull ArcheType Step ***')
                    dir('subDir') {​​​​​​​
                        checkout([$class: 'GitSCM', branches: [[name: "*/develop"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: "jenkins", url: "https://github.paypal.com/PP-INTEGRATION-MULESOFT/proxy-autogen-scripts.git"]]])
                    }​​​​​​​
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​
/**
        stage('Exchange Publish') {​​​​​​​
            steps {​​​​​​​
                publishExchangeProject(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", token: token, userId: "${​​​​​​​ANYPOINT_CREDENTIALS_USR}​​​​​​​")
            }​​​​​​​
        }​​​​​​​
*/
/**
        stage('Configure APIM') {​​​​​​​
            steps {​​​​​​​
                createAPIInstance(deploymentType: "CH", exchangeDetails: extractExchangeAssetDetail(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", assetId: "test-api", token: token, envId: "${​​​​​​​ANYPOINT_ENV_ID}​​​​​​​"))
            }​​​​​​​
        }​​​​​​​
*/
        stage('Apply API Policies') {​​​​​​​
            steps {​​​​​​​
                applyAPIPolicies(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", envId: "${​​​​​​​ANYPOINT_ENV_ID}​​​​​​​", token: token,  assetId: "ping", assetVersion: "1.0.2", apiInstance: getAPIInstanceDetails(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", assetId: "ping", assetVersion: "1.0.2", token: token, envId: "${​​​​​​​ANYPOINT_ENV_ID}​​​​​​​"))
            }​​​​​​​
        }​​​​​​​
/**

    
@Library('jenkins-shared-scripts@feature') _
import groovy.json.JsonSlurper
import groovy.json.JsonOutput


pipeline {​​​​​​​
    agent {​​​​​​​
        label "mesos"
    }​​​​​​​


    environment {​​​​​​​
        ANYPOINT_CREDENTIALS = credentials('anypoint.credentials.nonprod')
        ANYPOINT_ENV_ID = "10"
        ANYPOINT_ORG_ID = "2"
        ANYPOINT_GROUP_ID = "2c"
        MAVEN_HOME = "/opt/maven/maven-3.6.1/bin/"
        BUILD_VERSION = "test"
        ASSET_VERSION = "v1"
        TOKEN = getAnypointToken(username: "${​​​​​​​ANYPOINT_CREDENTIALS_USR}​​​​​​​", password: "${​​​​​​​ANYPOINT_CREDENTIALS_PSW}​​​​​​​")
        API_CONFIGS = ''
        CONTRIBUTORS = ''
        TARGET_ENV = ''
        HOST = ''
        PORT = ''
        API_ID = ''
        ZIPFILE = ''
        ENVIRONMENT = 'Development'
    }​​​​​​​


    tools {​​​​​​​
        maven 'Maven3.6.1'
        jdk 'ORACLEJDK8'
    }​​​​​​​





​[2/17 8:28 PM] Bishwadeep Das (AWF)
    
options {​​​​​​​
        //skips default checkout from Git whenever you enter a new stage with a new build agent.  DO NOT CHANGE
        skipDefaultCheckout()
    }​​​​​​​


    stages {​​​​​​​
    
        stage('Build') {​​​​​​​
            steps {​​​​​​​
                echo '*************************Build Started*************************************'
                checkout scm
            }​​​​​​​
        }​​​​​​​


        stage('Parse YAML') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    //Install yq
                    sh 'sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys CC86BB64'
                    sh 'sudo add-apt-repository ppa:rmescandon/yq'
                    sh 'sudo apt update'
                    sh 'sudo apt install yq -y'
                    sh 'yq --version'
                    
                    println('*** Parse YAML Step ***')
                    dir('apiSpec') {​​​​​​​
                        checkout([$class: 'GitSCM', 
                        branches: [[name: "*/master"]], 
                        doGenerateSubmoduleConfigurations: false, 
                        extensions: [], 
                        submoduleCfg: [], 
                        userRemoteConfigs: [[credentialsId: "jenkins", url: "https://github.paypal.com/PP-INTEGRATION-MULESOFT/API_Spec.git"]]])
                    }​​​​​​​
                    sh 'cp apiSpec/api-config.yml api-config.yml'


                    API_CONFIGS = readYaml file: 'api-config.yml'
                               println(API_CONFIGS)
                    
                    //Get environment info
                    switch (API_CONFIGS.environment) {​​​​​​​
                        case "PROD":
                            println("Retrieve PROD info")
                            HOST = API_CONFIGS.PROD.implHost
                            PORT = API_CONFIGS.PROD.implPort
                        case "QA":
                            println("Retrieve QA info")
                            HOST = API_CONFIGS.QA.implHost
                            PORT = API_CONFIGS.QA.implPort
                        default:
                            println("Retrieve DEV info")
                            HOST = API_CONFIGS.DEV.implHost
                            PORT = API_CONFIGS.DEV.implPort
                    }​​​​​​​
                    
                    // Get deployment target
                    TARGET_ENV = (API_CONFIGS.deploymentTarget.dataClassification < 4 || (API_CONFIGS.deploymentTarget.scalabilityNeeded == false && API_CONFIGS.deploymentTarget.apiLocation.toLowerCase() == "on-prem"))? "OP" : "CH"
                    println("Deploy to " + TARGET_ENV)
                    // Get users list
                    CONTRIBUTORS = '['
                    def userList = API_CONFIGS.userList.tokenize(',')
                    userList.each() {​​​​​​​
                        CONTRIBUTORS += '{​​​​​​​"userId":"' + it + '","role":"contributor"}​​​​​​​,'
                    }​​​​​​​
                    CONTRIBUTORS = CONTRIBUTORS.replaceAll(',$', '')
                    CONTRIBUTORS += ']'
                    println("Contributors: " + CONTRIBUTORS)
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​



    stage('Zip file') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    println("*** Zip Specification ***")
                    ZIPFILE = API_CONFIGS.assetId + ".zip"
                    if (fileExists(ZIPFILE)) {​​​​​​​
                        sh "rm $ZIPFILE"
                    }​​​​​​​
                    zip zipFile: ZIPFILE, dir: 'apiSpec'
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​
       
        stage('Pull ArcheType') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    println('*** Pull ArcheType Step ***')
                    dir('subDir') {​​​​​​​
                        checkout([$class: 'GitSCM',
                              branches: [[name: "*/develop"]],
                              doGenerateSubmoduleConfigurations: false,
                              extensions: [],
                              submoduleCfg: [],
                              userRemoteConfigs: [[credentialsId: "jenkins", url: "https://github.paypal.com/PP-INTEGRATION-MULESOFT/proxy-autogen-scripts.git"]]])
                        sh "mv mule4-rest-proxy-template/src/main/resources/*-dev.yaml mule4-rest-proxy-template/src/main/resources/${​​​​​​​API_CONFIGS.assetId}​​​​​​​-dev.yaml"
                        sh "mv mule4-rest-proxy-template/src/main/resources/*-qa.yaml mule4-rest-proxy-template/src/main/resources/${​​​​​​​API_CONFIGS.assetId}​​​​​​​-qa.yaml"
                        sh "mv mule4-rest-proxy-template/src/main/resources/*-prod.yaml mule4-rest-proxy-template/src/main/resources/${​​​​​​​API_CONFIGS.assetId}​​​​​​​-prod.yaml"
                    }​​​​​​​
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​
​[2/17 8:28 PM] Bishwadeep Das (AWF)
    
stage('Exchange Publish') {​​​​​​​
            steps {​​​​​​​
                publishToExchange(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", assetId: API_CONFIGS.assetId, groupId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", assetVersion: API_CONFIGS.version, token: "${​​​​​​​TOKEN}​​​​​​​", filePath: "${​​​​​​​ZIPFILE}​​​​​​​", envId: "${​​​​​​​ANYPOINT_ENV_ID}​​​​​​​")
            }​​​​​​​
        }​​​​​​​
         
        stage('Update Exchange') {​​​​​​​
            steps {​​​​​​​
                applyExchangeContributor(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", assetId: API_CONFIGS.assetId, groupId: "${​​​​​​​ANYPOINT_GROUP_ID}​​​​​​​", token: "${​​​​​​​TOKEN}​​​​​​​", users: CONTRIBUTORS)
            }​​​​​​​
        }​​​​​​​
         
        stage('Configure APIM') {​​​​​​​
            steps {​​​​​​​
                createAPIInstance(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", envId: "${​​​​​​​ANYPOINT_ENV_ID}​​​​​​​", token: "${​​​​​​​TOKEN}​​​​​​​", deploymentType: "${​​​​​​​TARGET_ENV}​​​​​​​", assetId: API_CONFIGS.assetId, assetVersion: API_CONFIGS.version, groupId: "${​​​​​​​ANYPOINT_GROUP_ID}​​​​​​​", uri: "${​​​​​​​HOST}​​​​​​​", port: "${​​​​​​​PORT}​​​​​​​")
            }​​​​​​​
        }​​​​​​​


        stage('Apply API Policies') {​​​​​​​
            steps {​​​​​​​
                applyAPIPolicies(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", envId: "${​​​​​​​ANYPOINT_ENV_ID}​​​​​​​", token: "${​​​​​​​TOKEN}​​​​​​​", assetId: API_CONFIGS.assetId, assetVersion: API_CONFIGS.version, apiInstance: getAPIInstanceDetails(orgId: "${​​​​​​​ANYPOINT_ORG_ID}​​​​​​​", assetId: API_CONFIGS.assetId, assetVersion: API_CONFIGS.version, token: "${​​​​​​​TOKEN}​​​​​​​", envId: "${​​​​​​​ANYPOINT_ENV_ID}​​​​​​​"))
            }​​​​​​​
        }​​​​​​​



​[2/17 8:29 PM] Bishwadeep Das (AWF)
    stage('Create Proxy') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    //Run script
                    sh "mv api-config.yml ${​​​​​​​API_CONFIGS.assetId}​​​​​​​.yaml"
                    sh "rm -rf subDir/propertiesandscripts/inputFiles/*.yaml"
                    sh "cp ${​​​​​​​API_CONFIGS.assetId}​​​​​​​.yaml subDir/propertiesandscripts/inputFiles/"
                    sh 'chmod +rx subDir/propertiesandscripts/src/script.sh'
                    def workspace = sh(script: "pwd", returnStdout: true)
                    workspace = workspace.trim() + "/subDir"
                    sh "subDir/propertiesandscripts/src/script.sh $API_ID $workspace"
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​

        stage('Pull Gateway Arcehtype') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    println('*** Pull Gateway Arcehtype ***')
                    dir('domain') {​​​​​​​
                        checkout([$class: 'GitSCM',
                              branches: [[name: "*/develop"]],
                              doGenerateSubmoduleConfigurations: false,
                              extensions: [],
                              submoduleCfg: [],
                              userRemoteConfigs: [[credentialsId: "jenkins", url: "https://github.paypal.com/PP-INTEGRATION-MULESOFT/proxy-gateway.git"]]])
                        configFileProvider([configFile(fileId: 'f7c96d11-af02-4f21-bb14-ce4c5e471d79', variable: 'MAVEN_SETTINGS_XML')]) {​​​​​​​
                            sh 'mvn -s $MAVEN_SETTINGS_XML clean install'
                        }​​​​​​​
                    }​​​​​​​
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​
​[2/17 8:29 PM] Bishwadeep Das (AWF)
    
stage('Deploy Proxy') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    println('*** Deploy Proxy Step ***')
                    //Package project
                    sh 'echo package $ENVIRONMENT'
                    sh 'chmod -R 777 ./'
                    sh "cp -a subDir/propertiesandscripts/inputFiles/${​​​​​​​API_CONFIGS.assetId}​​​​​​​/. subDir/code/"
                    sh 'chmod -R 777 subDir/code'
                                        
                    //def env = API_CONFIGS.environment.toLowerCase()                
                    configFileProvider([configFile(fileId: 'f7c96d11-af02-4f21-bb14-ce4c5e471d79', variable: 'MAVEN_SETTINGS_XML')]) {​​​​​​​
                        sh "mvn -f subDir/code -s $MAVEN_SETTINGS_XML clean package deploy -DmuleDeploy -DconnectedApp.ClientId=$ANYPOINT_CREDENTIALS_USR -DconnectedApp.ClientSecret=$ANYPOINT_CREDENTIALS_PSW -Dop.environment=$ENVIRONMENT -Denv=dev"
                    }​​​​​​​
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​        


        stage('Email Producer') {​​​​​​​
            steps {​​​​​​​
                script {​​​​​​​
                    println('*** Email Producer Step ***')
                    sendEmail(emailRecipients: API_CONFIGS.apiOwner)
                }​​​​​​​
            }​​​​​​​
        }​​​​​​​
    }​​​​​​​
    post {​​​​​​​
        always {​​​​​​​
            println('*** Send Email ***')
            sendEmail(emailRecipients: API_CONFIGS.apiOwner)
        }​​​​​​​
    }​​​​​​​
}​​​​​​​


//Method to obtain Anypoint token
def getAnypointToken(Map props) {​​​​​​​
    println("START getAnypointToken")
    def clientId = props.username
    def clientSecret = props.password
    def urlString = "https://anypoint.mulesoft.com/accounts/api/v2/oauth2/token"
    def message = 'client_id=' + clientId + '&client_secret=' + clientSecret + '&grant_type=client_credentials'
    def headers=["Content-Type":"application/x-www-form-urlencoded", "Accept": "application/json"]
    def connection = doRestHttpCall(urlString, "POST", message, headers)
    println("Response: " + connection.responseCode)
    if (connection.responseCode ==~'2..') {​​​​​​​}​​​​​​​ else {​​​​​​​
        throw new Exception("Failed to get the login token!")
    }​​​​​​​
    def response = "${​​​​​​​connection.content}​​​​​​​"
    def token = new JsonSlurper().parseText(response).access_token
    println("Bearer Token: " + token)
    println("END getAnypointToken")
    return token
}​​​​​​​
 




​[2/17 8:29 PM] Bishwadeep Das (AWF)
    
//Method to get API Instance
def getAPIInstanceDetails(Map props) {​​​​​​​
    println("START getAPIInstanceDetails")
    def urlString = "https://anypoint.mulesoft.com/apimanager/api/v1/organizations/" + props.orgId + "/environments/" + props.envId + "/apis?assetId=" + props.assetId + "&assetVersion=" + props.assetVersion
    def headers = ["Authorization": "Bearer " + props.token, "Accept": "application/json"]
    def connection = doRestHttpCall(urlString, "GET", null, headers)


    def response = "${​​​​​​​connection.content}​​​​​​​"


    if (connection.responseCode ==~'2..') {​​​​​​​
        println("the API instance retrieved successfully! statusCode=" + connection.responseCode)
    }​​​​​​​
    else {​​​​​​​
        throw new Exception("Failed to retreive API Instance! statusCode=${​​​​​​​connection.responseCode}​​​​​​​ responseMessage=${​​​​​​​response}​​​​​​​")
    }​​​​​​​
    def apiInstanceDetails = new JsonSlurper().parseText(response)
    def apiVersionId = apiInstanceDetails.assets[0].apis.find {​​​​​​​ it.assetVersion == props.assetVersion}​​​​​​​
    println("apiVersionId for env is " + apiVersionId);
    println("END getAPIInstanceDetails")
    API_ID = apiVersionId.id
    return apiVersionId.id;
}​​​​​​​


//Method to get API Instance
def publishToExchange(Map props) {​​​​​​​
    println("START publishToExchange")
    def response = sh(script: "curl -v \
        -H \"Authorization: Bearer ${​​​​​​​props.token}​​​​​​​\" \
        -F organizationId=${​​​​​​​props.orgId}​​​​​​​ \
        -F groupId=${​​​​​​​props.groupId}​​​​​​​ \
        -F assetId=${​​​​​​​props.assetId}​​​​​​​ \
        -F version=${​​​​​​​props.assetVersion}​​​​​​​ \
        -F name=${​​​​​​​props.assetId}​​​​​​​ \
        -F main=\"swagger.json\" \
        -F apiVersion=\"1.0.0\" \
        -F classifier=\"oas\" \
        -F asset=@${​​​​​​​props.filePath}​​​​​​​ \
        https://anypoint.mulesoft.com/exchange/api/v1/assets", returnStdout: true)
    
    println("RESPONSE: ")
    def publishProjectDetails = new JsonSlurper().parseText(response)
    if (publishProjectDetails.status != null && publishProjectDetails.status !=~'2..') {​​​​​​​
        throw new Exception("Failed to publish to Exchange! statusCode=${​​​​​​​publishProjectDetails.status}​​​​​​​ responseMessage=${​​​​​​​publishProjectDetails.message}​​​​​​​")
    }​​​​​​​
    else {​​​​​​​
        println("Publish to Exchange successfully! statusCode=201")
    }​​​​​​​
    println("Project details on exchange publish " + publishProjectDetails);
    println("END publishToExchange")
    return publishProjectDetails
}​​​​​​​



//Method to apply Exchange Contributor permissions
def applyExchangeContributor(Map props) {​​​​​​​
    println("START applyExchangeContributor")


    def message = JsonOutput.toJson(props.users)
    println("Creating a apply permissions operation request with this body: '" + message + "'")
    def urlString = "https://anypoint.mulesoft.com/exchange/api/v1/organizations/" + props.orgId + "/assets/" + props.groupId + "/" + props.assetId + "/users"
    println("Create apply permissions url: '" + urlString + "'")
    def headers = ["Content-Type": "application/json", "Authorization": "Bearer " + props.token]
    def connection = doRestHttpCall(urlString, "PUT", message, headers)
    def response = "${​​​​​​​connection.content}​​​​​​​"


    if (connection.responseCode ==~'2..') {​​​​​​​
        println("the API instance retrieved successfully! statusCode=" + connection.responseCode)
    }​​​​​​​
    else {​​​​​​​
        throw new Exception("Failed to publish to Exchange! statusCode=${​​​​​​​connection.responseCode}​​​​​​​ responseMessage=${​​​​​​​response}​​​​​​​")
    }​​​​​​​
    def applyPermissionsResponse = new JsonSlurper().parseText(response)
    println("Response on permissions application " + applyPermissionsResponse);
    println("END applyExchangeContributor")


    return applyPermissionsResponse;
}​​​​​​​









