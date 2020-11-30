pipeline {

	agent none


    stages {
        stage('worker build') {
			agent {
    		    docker {
 		           image 'maven:3.6.1-jdk-8-alpine'
        		    args '-v $HOME/.m2:/root/.m2'
		        }
   			}
			when {
				changeset "**/worker/**"
			}
            steps {
                echo 'Compiling worker app'
				dir('worker') {
					sh 'mvn compile'
				}
            }
        }
        stage('worker test') {
			agent {
    		    docker {
 		           image 'maven:3.6.1-jdk-8-alpine'
        		    args '-v $HOME/.m2:/root/.m2'
		        }
   			}
			when {
				changeset "**/worker/**"
			}
            steps {
                echo 'Running Unit Test on worker app'
				dir('worker') {
					sh 'mvn clean test'
				}
            }
        }
        stage('worker package') {
			agent {
    		    docker {
 		           image 'maven:3.6.1-jdk-8-alpine'
        		    args '-v $HOME/.m2:/root/.m2'
		        }
   			}
			when {
				branch 'master'
				changeset "**/worker/**"
			}
            steps {
                echo 'Packaging worker app'
				dir('worker') {
					sh 'mvn package -DskipTests'
            		archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
				}
            }
        }
        stage('worker-docker-package') {
			agent any
			when {
				branch 'master'
				changeset "**/worker/**"
			}
            steps {
                echo 'Packaging worker app with docker'
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def workerImage = docker.build("jackmontyorg/worker:v${env.BUILD_ID}", "./worker")
						workerImage.push()
						workerImage.push("${env.BRANCH_NAME}")
					}
				}
            }
        }

        stage('result build') {
	    	agent {
				docker {
					image 'node:8.16.0-alpine'
				}
			}
			when {
				changeset "**/result/**"
			}
            steps {
                echo 'Compiling result app'
				dir('result') {
					sh 'npm install'
				}
            }
        }
        stage('result test') {
		    agent {
				docker {
					image 'node:8.16.0-alpine'
				}
			}
			when {
				changeset "**/result/**"
			}
            steps {
                echo 'Running Unit Test on result app'
				dir('result') {
					sh 'npm install'
					sh 'npm test'
				}
            }
        }
		stage('result-docker-package') {
			agent any
			when {
				branch 'master'
				changeset "**/result/**"
			}
            steps {
                echo 'Packaging result app with docker'
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def resultImage = docker.build("jackmontyorg/result:v${env.BUILD_ID}", "./result")
						resultImage.push()
						resultImage.push("${env.BRANCH_NAME}")
					}
				}
            }
        }

        stage('vote build') {
    		agent {
				docker {
				image 'python:2.7.16-slim'
				args '--user root'
				}
			}
			when {
				changeset "**/vote/**"
			}
            steps {
                echo 'Compiling vote app'
				dir('vote') {
					sh 'pip install -r requirements.txt'
				}
            }
        }
        stage('vote test') {
		    agent {
				docker {
					image 'python:2.7.16-slim'
					args '--user root'
				}
			}
			when {
				changeset "**/vote/**"
			}
            steps {
                echo 'Running Unit Test on vote app'
				dir('vote') {
					sh 'pip install -r requirements.txt'
					sh 'nosetests -v'
				}
            }
        }
        stage('vote integration') {
		agent any
		when {
			changeset "**/vote/**"
			branch 'master'
		}
            	steps {
                	echo 'Running Integration Tests on vote app'
			dir('vote') {
				sh 'integration_test.sh'
			}
            	}
        }
		stage('vote-docker-package') {
			agent any
			when {
				branch 'master'
				changeset "**/vote/**"
			}
            steps {
                echo 'Packaging vote app with docker'
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def voteImage = docker.build("jackmontyorg/vote:v${env.BUILD_ID}", "./vote")
						voteImage.push()
						voteImage.push("${env.BRANCH_NAME}")
					}
				}
            }
        }

		stage('Sonarqube') {
			agent any
			when {
				branch 'master'
			}
			environment{
				sonarpath = tool 'SonarScanner'
			}
			steps {
				echo 'Running Sonarqube Analysis..'
				withSonarQubeEnv('sonar') {
					sh "${sonarpath}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
				}
			}
		}

		stage('deploy to dev') {
			agent any
			when {
				branch 'master'
			}
			steps {
				echo 'Deploy instavote app with docker compose'
				sh 'docker-compose up -d'
			}
		}
    }
    post {
        always {
			echo 'Pipeline for instavote app is complete ... '
        }
        failure {
			slackSend ( channel: "instavote-cd", message: "Build Failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
        }
        success {
			slackSend ( channel: "instavote-cd", message: "Build Success - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
        }
    }
}
