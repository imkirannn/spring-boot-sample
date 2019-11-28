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
	app = readMavenPom().getArtifactId()
    	ver = readMavenPom().getVersion()
        echo "APP NAME: ${app}"
        echo "VERSION: ${ver}"
     }
     stage('Build') {
            withMaven(maven: 'Maven 3') {
                sh '''#!/bin/bash
 		echo "Maven Clean install with test cases..."
                mvn -B -Dmaven.test.skip=true clean install
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
  withEnv(['registry=imkirann',
               'registryCredential=docker-hub-credentials'
               ]) {


 stage('Pull image') {
      echo "Pulling image: ${myapp}"
      myapp.pull() 


 	}
 stage('Run app') {
	sh "echo Deploying $app with $ver as a container from $registry on DockerHub......."
	sh """#!/bin/bash
	if [  -n "\$(docker ps  -aqf name=\$app)" ];then
	echo "Container $app exists , Deleting and Recreating....."
	docker rm -f "$app"
	else
	echo "Container $app doesn't exists on this machine... Creating "
	fi
	"""
	sh "docker run -ti -d --name $app -p 8080:8080  $registry/$app:$ver"
        sh "docker images -q -f dangling=true | xargs --no-run-if-empty docker rmi"
          
	}
 }
}
