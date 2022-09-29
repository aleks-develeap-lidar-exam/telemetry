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
            script {
                configFileProvider([configFile(fileId: 'exam_maven_settings', variable: 'SETTINGS')]) {
                    sh "mvn package"
            }
            env.GIT_MESSAGE = sh (
                script: "git log --format=%B -n 1 $GIT_COMMIT",
                returnStdout: true
                ).trim()
            if ("${GIT_MESSAGE}".contains('#e2e')){
                echo "e2e tests here"
            }
            }

        }
        }
 
    stage('Build') {
      steps {
        sh "mvn verify"
      }
    }

    stage('Publish') {
        when {
            branch "main"
        }
        steps {
            configFileProvider([configFile(fileId: 'exam_maven_settings', variable: 'SETTINGS')]) {
            sh "mvn deploy -s $SETTINGS -Dmaven.test.skip"
            }
        }
    }

    }
}

