currentBuild.displayName="WEBPAPP-#"+currentBuild.number
pipeline{
    agent any
 //   options {
   //   timeout(time: 1, unit: 'MINUTES') 
    //     }
    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "3.14.251.17:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "harindra"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "harindra_nexus3"
       
    }
 
    stages{
        stage("Checkout-SCM")
        {
            steps{
              
                cleanWs()
                checkout([$class: 'GitSCM',
                branches: [[name: '*/master']], 
                doGenerateSubmoduleConfigurations: false,
                extensions: [[$class: 'CleanBeforeCheckout']], 
                submoduleCfg: [], 
                userRemoteConfigs: [[credentialsId: 'github_credentials', 
                url: 'https://github.com/HariReddy910/MVN-Project.git']]])
                echo "Download finished form SCM"
                sh 'mvn -v'
             
            }
      
     }       
             
               
      //         stage('build && SonarQube analysis') {
       //     steps {
        //        withSonarQubeEnv('SonarQube') {
                    // Optionally use a Maven environment you've configured already
          //          withMaven(maven:'Maven 3.6.0') {
          //              sh 'mvn clean package sonar:sonar'
          //          }
          //      }
       //     } } 
           //        stage("Quality Gate") {
         //   steps {
          //      timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
           //         waitForQualityGate abortPipeline: true
           //     }
        //    }
      //  }
        

              //  sh 'mvn clean package '
                  //archiveArtifacts '**/*.war'
                 // sh 'mv /var/lib/jenkins/workspace/MVN-Project/target/app.war app-1.0.war'
             
         
        stage("archiveArtifacts "){
               steps {

             
                  archiveArtifacts '**/*.war'
                 // sh 'mv /var/lib/jenkins/workspace/MVN-Project/target/app.war app-1.0.war'
              } 
     }
        
    

        
        stage("Deployment-AppServer"){
            steps{
              echo "hi"
             sh label: '', script: 'scp /var/lib/jenkins/workspace/Webpage/target/app.war ubuntu@172.31.41.179:/home/ubuntu/apache-tomcat-9.0.37/webappsMVN.war'
           }
       
          }
    
         stage("publish to nexus") {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                       pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;
                    echo "harindra"
                     if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: "${pom.version}",
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        
     
            }
          }
}
