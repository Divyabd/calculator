
pipeline{
   agent  {
    docker {
        
      image 'maven:3.6.3-jdk-11'
      args '-v /root/.m2:/root/.m2'
     
    }
  }
   environment{
         AWS_REGION='us-west-2'
         AWS_DEFAULT_REGION='us-west-2'
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
               
                sh 'mvn sonar:sonar'
              }
            }
              post{
       failure{
          //  mail bcc: '', body: 'build & SonarQube analysis stage fails ', cc: '', from: '', replyTo: '', subject: 'build & SonarQube analysis step fails', to: 'divyagowdadivya238@gmail.com'
      echo "failure"
       }
                
           
     }
          }
    
   stage('SonarQube Quality Gate') { 
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

     
     stage('packing'){
      steps{
        echo 'package'
        sh 'mvn package'
        
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
      stage('Deploy to S3 Bucket'){
                steps{
                   withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'ce6d3a43-31cd-46d8-9776-27e111e74c05', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']])
          {
                       // s3Upload(file:'C:/Users/Assignment-0.0.1-SNAPSHOT', bucket:'awsbuketdivya', path:'sample/Assignment-0.0.1-SNAPSHOT')
   
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
                             "target": "targetdownload/"
                        }
                     ]
                }''',
 
  
   
)
     }
            post{
               success{
                 build job: 'check connection'
                  //quietPeriod:2
               mail bcc: '', body: 'build was successful and your file is now now uploading to vm ', cc: '', from: '', replyTo: '', subject: 'build successful', to: 'divyagowdadivya238@gmail.com'

        }
               }
            }
          
    }
   
  
}

