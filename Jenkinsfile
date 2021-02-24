
pipeline{
   agent {
    docker {
      image 'maven:3.6.3-jdk-11'
      args '-v /root/.m2:/root/.m2'
    }
  }
  stages {
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
            mail bcc: '', body: 'successfully done ', cc: '', from: '', replyTo: '', subject: 'build status', to: 'divyagowdadivya238@gmail.com'
        }
            success{
            mail bcc: '', body: 'successfully done ', cc: '', from: '', replyTo: '', subject: 'build status', to: 'divyagowdadivya238@gmail.com'
        }
     }
        
     }
    
          stage("build & SonarQube analysis") {
  
            steps {
              withSonarQubeEnv('sonarcloud') {
               
                sh 'mvn clean package sonar:sonar'
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
        }
    stage('collect artifact'){
     steps{
     archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
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
     }}
   
     
     
    }
  
}

