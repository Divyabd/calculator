
pipeline{
   agent  {
    docker {
      image 'maven:3.6.3-jdk-11'
      args '-v /root/.m2:/root/.m2'
    }
  }
 stages {
    stage('Clean'){
      steps{
        echo 'Clean'
        sh 'mvn clean'
        
      }
     }
     stage('Compile'){
      steps{
        echo 'Compile'
        sh 'mvn compile'
        
      }
     }
     
   stage('package'){
      steps{
        echo 'package'
        sh 'mvn package'
        
        
      }
     }
    stage('Test'){
      steps{
        echo 'Test'
        sh 'mvn test'
        
      }
         post{
       failure{
            mail bcc: '', body: 'test fails ', cc: '', from: '', replyTo: '', subject: 'build status', to: 'divyagowdadivya238@gmail.com'
        }
            
     }
        
     }
    
          stage("build & SonarQube analysis") {
  
            steps {
              withSonarQubeEnv('sonarcloud') {
               
                sh 'mvn clean package sonar:sonar'
              }
            }
              post{
       failure{
            mail bcc: '', body: 'build & SonarQube analysis stage fails ', cc: '', from: '', replyTo: '', subject: 'build & SonarQube analysis step fails', to: 'divyagowdadivya238@gmail.com'
        }
           
     }
          }
 /*  stage('SonarQube Quality Gate') { 
            steps{
                timeout(time: 1, unit: 'HOURS') { 
                    script{
                        def qg = waitForQualityGate() 
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                         }
                    }
                    
                }
            }
         post{
       failure{
            mail bcc: '', body: 'SonarQube Quality Gate stage fails ', cc: '', from: '', replyTo: '', subject: ' SonarQube agate  step fails', to: 'divyagowdadivya238@gmail.com'
        }
           
     }
        }
    stage('collect artifact'){
     steps{
     archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
     }
        post{
       failure{
            mail bcc: '', body: 'collect artifact stage fails ', cc: '', from: '', replyTo: '', subject: ' collect to artifactory fails', to: 'divyagowdadivya238@gmail.com'
        }
     }
    }
       stage('deploy to artifactory')
     {
     steps{
     
     rtUpload (
    serverId: 'ARTIFACTORY_SERVER',
    spec: '''{
          "files": [
            {
              "pattern": "target/*.jar",
              "target": "art-doc-dev-loc"
            }
         ]
    }''',
 
  
     )
     }
      post{
       failure{
            mail bcc: '', body: 'deploy to artifactory stage fails ', cc: '', from: '', replyTo: '', subject: 'deploy to artifactory fails', to: 'divyagowdadivya238@gmail.com'
        }
           
     }

     }*/
     stage('ssh-test'){
            steps{
                 sshagent(['de8791f1-fd17-4dce-8d8d-24d240c9d767']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.218.161.70'
                    echo "succesfull divya"
                 }
            }
        }
     stage('download from artifactory')
         {
            steps{
     
                rtDownload (
                    serverId: 'ARTIFACTORY_SERVER',
                    spec: '''{
                    "files": [
                         {
                             "pattern": "art-doc-dev-loc/**",
                             "target": "vs/"
                        }
                     ]
                }''',
 
  
   
)
     }
            post{
               success{
                  sshagent(['de8791f1-fd17-4dce-8d8d-24d240c9d767']){
                    sh 'scp -r /var/jenkins_home/workspace/calculator/target/*.jar ubuntu@54.218.161.70:/home/ubuntu/sample'
             //  mail bcc: '', body: 'build was successful ', cc: '', from: '', replyTo: '', subject: 'build successful', to: 'divyagowdadivya238@gmail.com'
                  }

        }
               }
            }
          
    }
  
}

