node {
    def mvnHome = tool name: 'Maven', type: 'maven'
    def mvnCli = "${mvnHome}/bin/mvn"

    //properties([
       // buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')),
        //disableConcurrentBuilds(),
        //[$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/deepuchakram/PetClinic.git/'],
       // [$class: 'ThrottleJobProperty', categories: [], limitOneJobWithMatchingParams: false, maxConcurrentPerNode: 0, maxConcurrentTotal: 0, paramsToUseForLimit: '', throttleEnabled: true, throttleOption: 'project'],
      //  pipelineTriggers([githubPush()]),
      //  parameters([string(defaultValue: 'DEV', description: 'env name', name: 'environment', trim: false)])
   // ])
    stage('Checkout SCM'){
        git branch: 'master', credentialsId: 'github-creds', url: 'https://github.com/deepuchakram/PetClinic'
    }
    stage('Read praram'){
        echo "The environment chosen during the Job execution is ${params.environment}"
        echo "$JENKINS_URL"
    }
    stage('Build') {
      shell 'mvn clean install -DskipTests'
    }
    
    stage('maven compile'){
        // def mvnHome = tool name: 'Maven', type: 'maven'
        // def mvnCli = "${mvnHome}/bin/mvn"
        shell "${mvnCli} clean compile"
    }

    stage('Unit Test') {
      shell 'mvn test'
    }

    stage('Integration Test') {
      shell 'mvn verify -DskipUnitTests'
    }
    
    
   stage("build & SonarQube analysis") {
        shell 'mvn clean package sonar:sonar'      
        }
            
    stage('maven package'){
        shell "${mvnCli} package -Dmaven.test.skip=true"
    }
    
    stage('maven deploy'){
        //shell "${mvnCli} deploy -Dmaven.test.skip=true"
        shell '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean deploy'
    }
   /* stage('Push To Nexus'){
        shell 'mvn clean package'
     // archiveArtifacts artifacts: 'target/*.war', onlyIfSuccessful: true
     /*nexusArtifactUploader artifacts: [[artifactId: 'spring-petclinic', classifier: '', file: 'target/petclinic-1.0-SNAPSHOT.war', 
                                       type: 'war']], 
        credentialsId: 'nexus', groupId: 'org.springframework.samples', nexusUrl: 'http://13.126.21.144:8081/', 
        nexusVersion: 'nexus3', protocol: 'http', repository: 'spring-petclinic', version: '4.2.5-SNAPSHOT' */
       // nexusArtifactUploader artifacts: [[artifactId: 'spring-petclinic', classifier: '', file: 'target/petclinic.war', type: 'war']], credentialsId: 'nexus', groupId: 'org.springframework.samples', nexusUrl: '13.126.21.144:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'spring-petclinic', version: '4.2.5-SNAPSHOT'
  } */
    stage('Archive artifacts') {
      archive 'target/*.war'
   }
    
    stage('Archive Test Results'){
        shell "mvn insall tomcat7:deploy"
        junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
    }
    
    stage('deploy') {
   		shell "cp -p **/*.war /opt/apache-tomcat-8.5.35/webapps"
   }
   stage('Deploy To Tomcat'){
     //   sshagent(['app-server']) {
      //      shell 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@ec2-52-70-39-48.compute-1.amazonaws.com:/opt/apache-tomcat-8.5.38/webapps/'
       sshagent(['tomcat'])
        shell 'scp -o StrictHostKeyChecking=no target/*.war ec2-13-127-201-210.ap-south-1.compute.amazonaws.com:8081 /opt/apache-tomcat-8.5.35/webapps'
}
   // }
   stage('Smoke Test'){
       sleep 5
      shell "curl ec2-52-70-39-48.compute-1.amazonaws.com:8080/petclinic"
   }

}
