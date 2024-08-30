#!/usr/bin/env groovy
def branch = "${env.BRANCH_NAME}"
pipeline {
	agent any
	options {
        ansiColor('xterm')
    }
	tools { 
        maven 'maven' 
    }
	environment { 
		LANG="en_US.UTF-8"
    }
    stages {
		stage ('Update POM') {
			steps {
				script { 
					if (branch == 'main'){
						sh '''
							export PRO_VERSION=$(mvn org.apache.maven.plugins:maven-help-plugin:3.2.0:evaluate -Dexpression=project.version -q -DforceStdout -s settings.xml)
							echo $PRO_VERSION
							mvn versions:set -DnewVersion="${PRO_VERSION}"-${BUILD_ID} -s settings.xml
							cat pom.xml
						'''
					} else {
						sh '''
							export PRO_VERSION=$(mvn org.apache.maven.plugins:maven-help-plugin:3.2.0:evaluate -Dexpression=project.version -q -DforceStdout -s settings.xml)
							echo $PRO_VERSION
							mvn versions:set -DnewVersion=${PRO_VERSION}-SNAPSHOT -s settings.xml
							cat pom.xml
						'''
					}
				}
			}
		}
		stage ('Build project') {
			steps {
				script { 
					if (branch == 'main' || branch == 'dev'){
						sh '''
							mvn clean deploy -s settings.xml
						'''
					} else {
						sh '''
							mvn clean install -s settings.xml
						'''
					}
				}
			}
		}
	}
	post {
		always {
			cleanWs()
		}
	}
}
