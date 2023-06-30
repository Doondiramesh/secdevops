node {
    def mvnHome
    stage('Preparation') { // for display purposes
        // Get some code from a GitHub repository
        git 'https://github.com/jglick/simple-maven-project-with-tests.git'
        // Get the Maven tool.
        // ** NOTE: This 'M3' Maven tool must be configured
        // **       in the global configuration.
        mvnHome = tool 'M3'
    }
    stage('Build') {
        // Run the maven build
        withEnv(["MVN_HOME=$mvnHome"]) {
            if (isUnix()) {
                sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
            } else {
                bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
            }
        }
    }
    stage('Results') {
        junit '**/target/surefire-reports/TEST-*.xml'
        // https://stackoverflow.com/questions/67162746/how-to-get-rid-of-noisy-warning-no-suitable-checks-publisher-found
	    // [Checks API] No suitable checks publisher found.
	    // junit skipPublishingChecks: true, '**/target/surefire-reports/TEST-*.xml'
        archiveArtifacts 'target/*.jar'
    }
    stage('Artifact upload to S3') {
        echo "Jar file uploading to S3 bucket"
        sh 'aws s3 cp target s3://ramesh-devops/builds/ --recursive --exclude "*" --include "*.jar"'
    }
}
