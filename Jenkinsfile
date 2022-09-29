pipeline {
    options {
        disableConcurrentBuilds()
        gitLabConnection gitLabConnection: 'gitlab', jobCredentialId: 'gitlab_apitoken'
        timeout(activity: true, time: 5)
    }
    
    tools {
        jdk 'jdk8'
        maven '3.6.2'
    }
    agent any

    stages {

    stage("Print env variables for debbuging") {
            steps {
                sh "printenv"
            }
        }

    stage('Feature test'){
        when {
                branch "feature/*"
            }

        steps{
            configFileProvider([configFile(fileId: 'exam_maven_settings', variable: 'SETTINGS')]) {
            sh "mvn package"
            }
        }
        }
 
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

