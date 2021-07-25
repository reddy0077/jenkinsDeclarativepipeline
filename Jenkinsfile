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
				sh "cp ROOT${env.BUILD_ID}.war mywebapp.war"
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
        
        stage("deploy war to tomcat server") {
            steps {
                sh "ssh root@ip-192-168-0-176 rm -f /var/lib/tomcat8/webapps/mywebapp.war"
				sh "scp mywebapp.war root@ip-192-168-0-176:/var/lib/tomcat8/webapps"
                
            }
        }
		stage("Deploy Image To DockerHost") {
            steps {
                sh "docker -H tcp://192.168.0.202:2375 stop prodwebapp1 || true"
                sh "docker -H tcp://192.168.0.202:2375 run --rm -dit --name prodwebapp1 --hostname prodwebapp1 -p 8000:8080 29207/varundocker0077:v${BUILD_NUMBER}"
                
            }
        }
		stage("Verify The Webapp") {
        steps {
          sh 'sleep 15s'
          sh 'curl http://192.168.0.202:8000'
          }
        }
    }
 }
