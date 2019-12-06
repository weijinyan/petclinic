pipeline {
   agent any

   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "M3"
      jdk 'jdk8'
   }

   stages {
     /* stage('github scm') {
         steps {
            // Get some code from a GitHub repository
            git credentialsId: 'b7515b53-9639-4ff6-976e-bd1c9dca6051', url: 'https://github.com/weijinyan/petclinic.git'
         }
      }*/
      stage('build'){
        steps{
            sh 'mvn clean compile test'
            sh 'mvn war:war'
            archiveArtifacts artifacts: 'target/*.war', fingerprint: true, onlyIfSuccessful: true
        }    
      }
     
       stage('QA Test'){
        steps{
             sh "mvn jacoco:dump -Djacoco.address=127.0.0.1 -Djacoco.append=false -Djacoco.reset=true"
          
            dir('/Users/liujie/workdir') {
                git credentialsId: 'b7515b53-9639-4ff6-976e-bd1c9dca6051', url: 'https://github.com/weijinyan/petclinicauto.git'
                sh 'mvn clean compile package'
                sh 'java -jar ./target/petclinicauto-0.0.1-SNAPSHOT-jar-with-dependencies.jar'
            }
            // input 'Is manual test finished?'
        }    
      }
      
      stage('Code Coverage(QA Test)'){
          steps{
                sh "mvn jacoco:dump -Djacoco.address=127.0.0.1 -Djacoco.append=false"
                sh "mvn jacoco:report"
                archiveArtifacts artifacts: 'target/site/jacoco/**/*', fingerprint: true
          }
      }
     
   }
}