currentBuild.displayName="MVN-Project-#"+currentBuild.number
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
        NEXUS_URL = "172.31.9.95:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "harindra"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "Harindra_NEXUS"
       
    }
 
    stages{
        stage("Checkout-SCM")
        {
            steps{
                script {
                cleanWs()
                checkout([$class: 'GitSCM',
                branches: [[name: '*/master']], 
                doGenerateSubmoduleConfigurations: false,
                extensions: [[$class: 'CleanBeforeCheckout']], 
                submoduleCfg: [], 
                userRemoteConfigs: [[credentialsId: 'github_credentials', 
                url: 'https://github.com/HariReddy910/MVN-Project.git']]])
                echo "Download finished form SCM"
                }
            }
        
        post{
              failure{
                  script { STAGE_FAILED = "checkout: failed to checkout the application.." }
              }
        }
           }       
             stage("Build "){
               steps {

                sh 'mvn clean package '
                  archiveArtifacts '**/*.war'
                 // sh 'mv /var/lib/jenkins/workspace/MVN-Project/target/app.war app-1.0.war'
              } 
          
         post{
              failure{
                  script {STAGE_FAILED = " Build : Failed to Build the application.." }
              }  
           }
        }
        stage("Build "){
               steps {

             
                  archiveArtifacts '**/*.war'
                 // sh 'mv /var/lib/jenkins/workspace/MVN-Project/target/app.war app-1.0.war'
              } 
           
         post{
              failure{
                  script {STAGE_FAILED = " archiveArtifacts :  archiveArtifacts failed.." }
              }
        }
     }
            stage('Sonarqube') {
    environment {
        scannerHome = tool 'SonarQube'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/opt/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
    

         post{
              failure{
                  script {STAGE_FAILED = "Reports: failed to generate Code quality checks..." }
              }
           }
        }
 }       
        stage("Deployment-AppServer"){
            steps{
              echo "hi"
             sh label: '', script: 'scp /var/lib/jenkins/workspace/MVN-Project/target/app.war ubuntu@172.31.2.23:/opt/tomcat9/webapps/MVN.war'
           }
         post{
              failure{
                  script {STAGE_FAILED = "Deploy Application : failed on application." }
              }
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
        
         post{
              failure{
                  script {STAGE_FAILED = "Nexus Publish : Failed on Nexus publish.." }
              }
            }
            }
          }
}
