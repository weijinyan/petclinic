pipeline {
   agent any

   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "M3"
      jdk 'jdk8'
   }

   stages {
      stage('github scm') {
         steps {
            // Get some code from a GitHub repository
            git url: 'https://github.com/weijinyan/petclinic.git'
         }
      }
      stage('compile'){
        steps{
            sh 'mvn clean compile'
        }    
      }
     stage('Dev - JUnit Test'){
         steps{
            sh "mvn jacoco:prepare-agent test" 
            junit "target/surefire-reports/*.xml"
         }
     }
     stage('Code Coverage (JUnit Test)'){
         steps{
              jacoco()
         }
     }
     stage('Code Quality(Sonar)'){
         steps{
             withSonarQubeEnv('MySonarQube'){
                sh 'mvn sonar:sonar'
             }
         }
     }
     stage('Quality Gate(Dev)'){
         steps{
             timeout(time:10, unit:'MINUTES'){
                 script{
                     def qg = waitForQualityGate()
                     if(qg.status !='OK'){
                         error 'Pipeline aborted due to quality date failure: {qg.status}'
                     }
                 }
             }
         }
     }
     stage('Build WAR'){
         steps{
            sh 'mvn war:war' 
            archiveArtifacts artifacts: 'target/*.war', fingerprint: true, onlyIfSuccessful: true
         }
     }
     stage('Deploy'){
         steps{
           dir(params.DeployScriptFolder){
                git branch: 'jwei', url: 'https://github.com/weijinyan/petclinic-deploy.git'
                sh "/Library/Frameworks/Python.framework/Versions/2.7/bin/ansible-playbook -i tests/inventory tests/test.yml"
            }
         }
     }
       stage('QA Test'){
        steps{
             sh "mvn jacoco:dump -Djacoco.address=52.146.56.80 -Djacoco.port=31000 -Djacoco.append=false -Djacoco.reset=true"
          
            dir(params.AutomationTestPath) {
                git url: 'https://github.com/weijinyan/petclinicauto.git'
                sh 'mvn clean compile package'
                sh 'java -jar ./target/petclinicauto-0.0.1-SNAPSHOT-jar-with-dependencies.jar'
            }
            // input 'Is manual test finished?'
        }    
      }
      
      stage('Code Coverage(QA Test)'){
          steps{
                sh "mvn jacoco:dump -Djacoco.address=52.146.56.80 -Djacoco.port=31000 -Djacoco.append=false"
                sh "mvn jacoco:report"
                archiveArtifacts artifacts: 'target/site/jacoco/qa-coverage/**/*', fingerprint: true
          }
      }
     stage('Quality Gate (QA)'){
         steps{
             sh 'mvn jacoco:check'
         }
     }
   }
}
