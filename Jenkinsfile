pipeline {
    environment {
        registry = '29207/varundocker0077'
		registryCredential = 'dockerhub_id'
        dockerImage = ''
	}
    agent any

    stages {
        stage('code Checkout') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'git@github.com:reddy0077/jenkinsDeclarativepipeline.git']]])
               sh "ls -al"
            }       
        }
        stage("Code Build") {
            steps {
                echo "compiling java code "
				sh "rm -f *.war"
                sh "./build.sh"
				sh "mv ROOT.war ROOT${env.BUILD_ID}.war"
				sh "ls -al"
            }
        }

        stage("Uploading artifacts to S3") {
            steps {
                echo "Uploading artifacts to S3"
                s3Upload consoleLogLevel: 'INFO', dontSetBuildResultOnFailure: false, dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 'uploadartifacts', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, managedArtifacts: false, noUploadOnFailure: false, selectedRegion: 'us-east-1', showDirectlyInBrowser: false, sourceFile: '*.war', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: false]], pluginFailureResultConstraint: 'FAILURE', profileName: 's3 artifact copy', userMetadata: []
                
            }
        }
        
        stage("Create Docker Image and push") {
            steps {
                script {  
                  	dockerImage = docker.build registry + ":v$BUILD_NUMBER"
                    docker.withRegistry('', registryCredential ) {
                        dockerImage.push()
                    }
                }
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
