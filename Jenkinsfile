#!/usr/bin/env groovy

String gitEmail = "hibaelnatour@gmail.com"
String gitUser = "hibaelnatour"
String gitRepoAdmin_URL = "https://github.com/hibaelnatour/project-admin.git" 
String apiCoprSSOEndpoint = "http://api-transformation-jenkins-iam-poc.apps.na39.openshift.opentlc.com/api/v1/corpsso"
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

		sh 'git config --global credential.helper cache'
		sh 'git config --global push.default simple'
		sh 'git config --global user.email hibaelnatour@gmail.com'
		sh 'git config --global user.name hibaelnatour'
		
		workspace = pwd()

		//Checkout Git repo project-admin 
		stage(name: 'Checkout'){
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
			
			response = httpRequest consoleLogResponseBody: true, contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: toJson(data), url: apiCoprSSOEndpoint, validResponseCodes: '200'		
		}
	}

} catch (caughtError) {

    err = caughtError
    currentBuild.result = "FAILURE"

}
