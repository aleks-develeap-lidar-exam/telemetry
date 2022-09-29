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
                script{
                    GIT_MESSAGE = sh (
                    script: "git log --format=%B -n 1 $GIT_COMMIT",
                    returnStdout: true
                    ).trim()
                    if ("${GIT_MESSAGE}".contains('#e2e')){
                        env.E2E_TEST = true
                        
                    }
                }
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
    
    stage('E2E test'){
        when {
            anyOf {
                branch 'main'
                branch 'release/*'
                allOf {
                    environment name: 'ENV_TEST', value: 'true'
                    branch 'feature/*'
                }
            }     
        } 
        steps{
            echo "E2E tests here"
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

