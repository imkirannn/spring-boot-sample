node ('master') {
    withEnv(['registry=imkirann/cp-minds',
               'registryCredential=docker-hub-credentials'
               ]) {
    def myapp

    stage('Clone repository') {

        checkout scm
    }
     stage('Build') {
            app = readMavenPom().getArtifactId()
	    ver = readMavenPom().getVersion()
            withMaven(maven: 'Maven 3') {
                sh '''#!/bin/bash 
                mvn -B -Dmaven.test.skip=true clean install
               // export app=$(mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.artifactId -q -DforceStdout)
               // export ver=$( mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.version -q -DforceStdout )
                echo "app is $app"
                 echo "deploying with version $ver"
                 '''
            }
        }
      stage('Image') {
            myapp = docker.build registry + // + "$app" + ":$ver"
           }

 }
}
