node ('master') {
    def myapp

    stage('Clone repository') {

        checkout scm
    }
     stage('Build') {
            withMaven(maven: 'Maven 3') {
                sh '''
                mvn -B -Dmaven.test.skip=true clean install
                export app=$(mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.artifactId -q -DforceStdout)
                export ver=$( mvn org.apache.maven.plugins:maven-help-plugin:3.1.0:evaluate -Dexpression=project.version -q -DforceStdout )
                echo $app
                 echo $ver
                '''
            }
        }
}
