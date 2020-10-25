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
    stage('maven compile'){
        // def mvnHome = tool name: 'Maven', type: 'maven'
        // def mvnCli = "${mvnHome}/bin/mvn"
        shell "${mvnCli} clean compile"
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
stage('Archive atifacts'){
sh script: 'mvn clean package'
      archiveArtifacts artifacts: 'target/*.war', onlyIfSuccessful: true
        
  }
        stage('Archive Test Results'){
        shell "mvn insall tomcat7:deploy"
        junit allowEmptyResults: true, testResults: '**/surefire-reports/*.xml'
    }
   stage('Deploy To Tomcat'){
     //   sshagent(['app-server']) {
      //      shell 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@ec2-52-70-39-48.compute-1.amazonaws.com:/opt/apache-tomcat-8.5.38/webapps/'
      //  
shell 'scp -o StrictHostKeyChecking=no target/*.war ec2-15-206-211-108.ap-south-1.compute.amazonaws.com:/root/tomcat9/webapps/'
}
   // }
   stage('Smoke Test'){
       sleep 5
      shell "curl ec2-52-70-39-48.compute-1.amazonaws.com:8080/petclinic"
   }

}
