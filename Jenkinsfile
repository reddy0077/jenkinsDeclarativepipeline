pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'git@github.com:reddy0077/jenkinsDeclarativepipeline.git']]])
               sh "ls -al"
            }       
        }
        stage("Build") {
            steps {
                echo "Some code compilation here..."
                echo "${env.BRANCH_NAME}"
                sh "ls -al"
            }
        }

        stage("Test") {
            steps {
                echo "Some tests execution here..."
                echo "${env.BUILD_NUMBER}"
                
            }
        }
        
        stage("Deploy") {
            steps {
                echo "Some tests execution here..."
                echo "${env.BUILD_ID}"
                
            }
        }
        
        stage("Verify") {
            steps {
                echo "Some tests execution here..."
                echo "${env.JENKINS_HOME}"
                echo "1"
                
            }
        }
    }
}

