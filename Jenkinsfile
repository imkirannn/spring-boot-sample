def myapp
node ('master') {
    withEnv(['registry = https://768749700464.dkr.ecr.eu-west-2.amazonaws.com',
               'ECRCRED = ecr:eu-west-2:amazon-ecr-jenkins'
               ]) {


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
            myapp = docker.build("cloudhands" + "/" + "$app" + ":$ver")
            
            }
     stage('Push image') {
     	sh("eval \$(aws ecr get-login --no-include-email | sed 's|https://||')")
        docker.withRegistry('https://768749700464.dkr.ecr.eu-west-2.amazonaws.com', 'ecr:eu-west-2:amazon-ecr-jenkins') {
        myapp.push()
        }
    }

 }

}

