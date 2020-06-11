currentBuild.displayName="MVN-Project-#"+currentBuild.number
pipeline{
    agent any
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
            }
        }
             stage("Build "){
               steps {

                sh 'mvn clean package '
                  archiveArtifacts '**/*.war'
              } 
            }
        stage("Deployment-AppServer"){
            steps{
              echo "hi"
             sh label: '', script: 'scp /var/lib/jenkins/workspace/MVN-Project/target/app.war ubuntu@172.31.2.23:/opt/tomcat9/webapps/MVN.war'
           }
      }
        
        
         stage("publish to nexus") {
            steps {
               
          nexusArtifactUploader artifacts: [
          [
              artifactId: 'app', 
             classifier: '', 
              file: 'target/app.war', 
               type: 'war'
            ]
          ], 
      credentialsId: 'new_nexus',
      groupId: 'Scriptbees',
      nexusUrl: '13.59.15.204:8081', 
      nexusVersion: 'nexus3',
      protocol: 'http', 
       repository: 'http://13.59.15.204:8081/repository/release/', 
      version: '1.0'
           
          }
      }
    }
 }
