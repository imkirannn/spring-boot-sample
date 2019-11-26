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
 stage('Pull image') {
      echo "Pulling image: ${myapp}"
      myapp.pull() 


 }
 stage('Run app') {
        echo "app is ${app} and version here is ${ver}"
        sh '''#!/bin/bash 
        if [[ $(docker inspect -f '{{.State.Running}}' "$app") == 'true' ]];then
        	docker rm -f "$app"
	fi;
	docker run -ti -d --name "$app" -p 8080:8080  "$registry"/"$app":"$ver"
        '''      
}
}
