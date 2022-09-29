pipeline {
    options {
        disableConcurrentBuilds()
        gitLabConnection gitLabConnection: 'gitlab', jobCredentialId: 'gitlab_apitoken'
        timeout(activity: true, time: 5)
    }
    
  agent any

  stages {
 
    stage('Build') {
      steps {
        sh "mvn verify"
      }
    }

    stage('Publish') {
        steps {
            configFileProvider([configFile(fileId: 'exam_maven_settings', variable: 'SETTINGS')]) {
            sh "mvn deploy -s $SETTINGS -Dmaven.test.skip"
            }
        }
    }

    }
}
