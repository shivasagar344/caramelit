currentBuild.displayName="MVN-Project-#"+currentBuild.number
pipeline{
    agent any
 //   options {
   //   timeout(time: 1, unit: 'MINUTES') 
    //     }
 
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
            }
          }
