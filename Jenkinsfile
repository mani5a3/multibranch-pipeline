pipeline {
	agent any
    options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
	}
	parameters {
		choice(name: 'branchname', choices: ['master', 'mani_dev', 'test_dev'], description: 'select the branch name')
		booleanParam(name: 'skip_stage', defaultValue: false, description: '')
	}
	stages {
		stage("clone") {
			steps {
			    script {
			        currentBuild.displayName = "declarative_${BUILD_NUMBER}"
        			git branch: "${params.branchname}", url: 'https://github.com/mani5a3/game-of-life.git'
			    }
			}
		}
		stage("build") {
			steps {
			bat 'mvn package'
			}
		}
		stage("publishing artifacts to jfrog") {
		   when {
		     expression {
			   params.skip_stage != true
			 }
		   }
			steps {
			  script {
			    def SERVER_ID = 'artifactory'
				def server = Artifactory.server SERVER_ID
				def uploadfiles = """{
					"files": [
						{
						  "pattern": "gameoflife-web/target/gameoflife.war",
						  "target": "example-repo-local"
						  
						},
						{
						  "pattern": "gameoflife-core/target/*.jar",
						  "target": "example-repo-local"
						  
						}
					]
					}"""
			    server.upload(uploadfiles)
			  }
			}
		}
	}
	post  {
	    always {  
             emailext (
			   subject: 'jenkins notification through pipeline',
			   body: '${BUILD_URL}',
			   to: 'sivakrishna5a35a3@outlook.com'
			 
			 ) 
         }
	}
}