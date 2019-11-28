def myapp
node ('master') {
    withEnv(['registry=imkirann',
               'registryCredential=docker-hub-credentials'
               ]) {
//    def myapp

    stage('Clone repository') {

        checkout scm
    }
     stage('read POM versions') {
//	app = readMavenPom().getArtifactId()
//    	ver = readMavenPom().getVersion()
    sh 'export ver="$(mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.version -q -DforceStdout)"'
    sh 'export app="$(mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.artifactId -q -DforceStdout)"'
        echo "IMAGE: ${app}"
        echo "VERSION: ${ver}"
     }
     stage('Build') {
            withMaven(maven: 'Maven 3') {
		sh "echo im here in sh of mvn, $app is $registry is ${ver} is"
                sh '''#!/bin/bash
   		 echo "immmm here in sh of mvn, ${app} is $registry is $ver is" 
                mvn -B -Dmaven.test.skip=true clean install
                 echo "app is $app"
                 echo "deploying with version $ver"
                 '''
            }
        }
     stage('Image') {
            myapp = docker.build registry + "/" + "$app" + ":$ver"

            }
     stage('Push image') {
        docker.withRegistry( '', registryCredential ) {
        myapp.push()
        }
    }

 }
}
/* Here logic comes for 
   deployment on app node
   added as a slave in jenkins
*/
node ('Remote') {
  stage('Checkout POM') {

        checkout scm
    }

 stage('Pull image') {
      echo "Pulling image: ${myapp}"
      myapp.pull() 


 }
 stage('Run app') {
        sh '''
        sh 'export ver="$(mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.version -q -DforceStdout)"'
        sh 'export app="$(mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.artifactId -q -DforceStdout)"'
	echo "Deploying $app with $ver as a container from $registry on DockerHub......."
	if [  -n "$(docker ps  -aqf name=$app)" ];then
	echo "Container $app exists , Deleting and Recreating....."
 	docker rm -f $app
        docker run -ti -d --name $app -p 8080:8080  $registry/$app:$ver
	'''
          
}
}
