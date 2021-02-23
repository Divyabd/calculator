
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
     }
    
          stage("build & SonarQube analysis") {
  
            steps {
              withSonarQubeEnv('sonarcloud') {
                sh 'java -version'
                sh 'mvn clean package sonar:sonar'
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
 
  
    buildName: 'holyFrog',
    buildNumber: '42'
)
     }}
    stage('download to artifactory')
   {
     steps {
       rtDownload (
                         serverId: 'ARTIFACTORY_SERVER',
                     spec: '''{
                             "files": [
                                      {
                                      "pattern": "art-doc-dev-loc/my-app-1.0-SNAPSHOT.jar",
                                      "target": "bazinga/"
                                    }
                                ]
                            }'''
                        )
                        }}
    }
}

