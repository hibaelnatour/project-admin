#!/usr/bin/env groovy

String gitEmail = "hibaelnatour@gmail.com"
String gitUser = "hibaelnatour"
String gitRepoAdmin_URL = "https://github.com/hibaelnatour/project-admin.git" 
String apiCoprSSOEndpoint = "http://api-transformation-myaccess-reg-jenk-poc.apps.na39.openshift.opentlc.com/api/v1/corpsso"
String workspace
String finalConfFilename

def toJson = {
	input ->
	groovy.json.JsonOutput.toJson(input)
}

def err = null
def response

currentBuild.result = "SUCCESS"

try {

	node {

		workspace = pwd()

		//Checkout Git repo project-admin 
		stage(name: 'Checkout'){
			sh 'git config user.email ${gitEmail}'
			sh 'git config user.name ${gitUser}'
			git url: gitRepoAdmin_URL, branch: 'master'
		}

		//Get finalConfFilename in jenkins workspace after git checkout
		stage(name: 'Get file'){
			def files = findFiles(glob: 'final.json') 
			echo "${files[0].name}"    	
			finalConfFilename = "${files[0].name}"
		}

		//Call API CorpSSO 
		stage(name: 'CorpSSO'){
			def data = readJSON file: finalConfFilename
			
			echo toJson(data)
			
			response = httpRequest consoleLogResponseBody: true, contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: toJson(data), url: ${apiCoprSSOEndpoint}, validResponseCodes: '200'
			

			//echo "..................."
			//sh "value=`cat test-conf.json`"
		}
	}

} catch (caughtError) {

    err = caughtError
    currentBuild.result = "FAILURE"

}