node ('master') {
    withEnv(['registry=imkirann/cp-minds',
               'registryCredential=docker-hub-credentials'
               ])
    }
    def myapp

    stage('Clone repository') {

        checkout scm
    }

    stage('Build') {
            withMaven(maven: 'Maven 3') {
                sh 'mvn -B -DskipTests clean install'
                sh 'export app=$(mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.artifactId -q -DforceStdout)'
                sh 'export ver=$( mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.version -q -DforceStdout )'
            }
        }
        stage('Image') {
            /* app = docker.build("imkirann/cp-minds/spring-boot-sample:2.0.2") */
            myapp = docker.build registry + "/$app" + ":$ver"

            }
        }

    stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
        app.push()
        }
    }
