pipeline {
    agent none

    stages {
    	
        stage('build') {
        agent{
        		docker{
		    	    image 'maven:3.6.1-jdk-8-alpine'
		    	    args '-v $HOME/.m2:/root/.m2'
		    	}
        	}
        when{
		changeset "**/worker/**"
	    }
            steps {
                echo 'Compiling worker app2'
                dir('worker')
                {
                    sh 'mvn compile'
                }
            }
        }
        stage('test') {
        agent{
        		docker{
		    	    image 'maven:3.6.1-jdk-8-alpine'
		    	    args '-v $HOME/.m2:/root/.m2'
		    	}
        	}
        when{
		    changeset "**/worker/**"
		}
            steps {
                echo 'Running Unit TEsts on worker app'
                dir('worker')
                {
                    sh 'mvn clean test'
                }                
            }
        }
        stage('package') {
        	agent{
        		docker{
		    	    image 'maven:3.6.1-jdk-8-alpine'
		    	    args '-v $HOME/.m2:/root/.m2'
		    	}
        	}
        
		when{
		    branch 'master'
		    changeset "**/worker/**"
		}
            steps {
                echo 'Packaging worker app2'
                dir('worker')
                {
                	
                    sh 'mvn package -DskipTests'                    
		     archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                }
            }
        }
        stage('docker-package') {
        	agent any
		when{
		    changeset "**/worker/**"
		    branch 'master'
		}
            steps {
                echo 'Packaging worker app with docker'
                script{
                	docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin')
                	{
                		def workerImage = docker.build("railgunlvl5/result:v${env.BUILD_ID}","./result")
                		workerImage.push()
               		workerImage.push("${env.BRANCH_NAME}")
                	}
                }
            }
        }
    }
    post{
	    always{
	    	echo 'Pipeline for worker is complete..'	
	    }
	    failure{
	    	slackSend (channel: "instavote-cd", message: "Build Failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
	    }
	    success{
	    	slackSend (channel: "instavote-cd", message: "Build Succeededd - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
	    }
    }
    
}

