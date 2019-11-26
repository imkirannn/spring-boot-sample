node ('master') {
    withEnv(['registry=imkirann/cp-minds',
               'registryCredential=docker-hub-credentials'
               ]) {
    def myapp

    stage('Clone repository') {

        checkout scm
    }
     stage('read POM versions') {
	app = readMavenPom().getArtifactId()
    	ver = readMavenPom().getVersion()
        echo "IMAGE: ${app}"
        echo "VERSION: ${ver}"
     }
     stage('Build') {
            withMaven(maven: 'Maven 3') {
                sh '''#!/bin/bash 
                mvn -B -Dmaven.test.skip=true clean install
                 echo "app is $app"
                 echo "deploying with version $ver"
                 '''
            }
        }
 }
}
