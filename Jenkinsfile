pipeline {
    // some block
 agent any
  stages {  
    stage('Github Checkout SCM') {
        steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/umamahiswari22/simple-java-maven-app.git']]])
        }}
    stage('Build') {
        steps{ 
            withMaven(maven: 'maven_3.6.3')
            {
            sh "mvn -B -DskipTests clean package"
            }
            
        } 
    }
     
    stage('Push JARS to artifactory') {
        steps {
        rtUpload (
    serverId: 'ARTIFACTORY_SERVER',
    spec: '''{
          "files": [
            {
              "pattern": "target/my-app-1.0-SNAPSHOT.jar",
              "target": "Test-maven-app/MAVEN-APP/JARS/${BUILD_NUMBER}/"
            }
         ]
    }''',
 )
    }
    }
    stage('deploy using asible tower')
    {
        environment 
        {
        COMMIT_MSG = sh (
              script: "git log --format=format:%s -1 | grep -Eo 'IPCC-[[:digit:]]+'",returnStdout: true)
        }
        steps
        {
            echo "${COMMIT_MSG}"
            ansibleTower async: false, credential: 'Demo Credential', extraVars: '''---
BUILD_NUMBER: ${BUILD_NUMBER}''', importTowerLogs: true, importWorkflowChildLogs: true, inventory: 'Demo Inventory', jobTags: '', jobTemplate: 'Jenkins demo Job Template', jobType: 'run', limit: '', removeColor: false, skipJobTags: '', templateType: 'job', throwExceptionWhenFail: false, towerCredentialsId: 'AnsibelTowerCredentials', towerServer: 'Anible Tower', verbose: true
           // ansibleTower async: false, credential: 'Demo Credential', extraVars: '', importTowerLogs: true, importWorkflowChildLogs: true, inventory: 'Demo Inventory', jobTags: '', jobTemplate: 'Demo Job Template', jobType: 'run', limit: '', removeColor: false, skipJobTags: '', templateType: 'job', throwExceptionWhenFail: false, towerCredentialsId: 'AnsibelTowerCredentials', towerServer: 'Anible Tower', verbose: false

    }
    post 
        {
         always 
            {
                jiraSendBuildInfo site: 'ipccprojects.atlassian.net', branch: "${COMMIT_MSG}-master"
            }
        }
    }   
   }
}
