pipeline {
	agent none

	stages{
		stage("build-worker"){
			when {
				changeset "**/worker/**"
			}
			agent {
				docker {
					image 'maven:3.6.1-jdk-8-slim'
					args '-v $HOME/.m2:/root/.m2'
				}
			}
			steps{
				echo 'Compiling worker app'
				dir('worker'){
					sh 'mvn compile'
				}
			}
		}
		stage("test-worker"){
			when {
				changeset "**/worker/**"
			}
			agent {
				docker {
					image 'maven:3.6.1-jdk-8-slim'
					args '-v $HOME/.m2:/root/.m2'
				}
			}
			steps{
				echo 'Running Unit Tets on worker app'
				dir('worker'){
					sh 'mvn clean test'
				}
			}
		}
		stage("package-worker"){
			when {
				branch 'master'
				changeset "**/worker/**"
			}
			agent {
				docker {
					image 'maven:3.6.1-jdk-8-slim'
					args '-v $HOME/.m2:/root/.m2'
				}
			}
			steps{
				echo 'Packaging worker app'
				dir('worker'){
					sh 'mvn package -DskipTests'
					archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
				}
			}
		}
		stage('docker-package-worker'){
			agent any
			when {
				branch 'master'
				changeset "**/worker/**"
			}
			steps{
				echo 'Packaging worker app with docker'
				script{
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def workerImage = docker.build("lbpaulino/worker:v${env.BUILD_ID}", "./worker")
						workerImage.push()
						workerImage.push("latest")
					}
				}
			}
		}

		stage("build-result"){
			when{
				changeset "**/result/**"
			}
			agent {
				docker {
					image 'node:8.9.0-slim'
					args '-v $HOME/.m2:/root/.m2'
				}
			}
			steps{
				echo 'Compiling result app'
				dir('result'){
					sh 'npm install'
				}
			}
		}
		stage("test-result"){
			when{
				changeset "**/result/**"
			}
			agent {
				docker {
					image 'node:8.9.0-slim'
					args '-v $HOME/.m2:/root/.m2'
               			}
        		}
			steps{
				echo 'Running Unit Tets on result app'
				dir('result'){
					sh 'npm install'
					sh 'npm test'
				}
			}
		}
		stage('docker-package-result'){
			agent any
			when {
				branch 'master'
				changeset "**/result/**"
			}
			steps{
				echo 'Packaging result app with docker'
				script{
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def resultImage = docker.build("lbpaulino/result:v${env.BUILD_ID}", "./result")
						resultImage.push()
						resultImage.push("latest")
					}
				}
			}
		}

		stage('build-vote') {
			when{
				changeset "**/vote/**"
			}
			agent {
				docker {
					image 'python:2.7.16-slim'
					args '--user root -v $HOME/.m2:/root/.m2'
				}
			}
			steps{
				echo "Compiling vote app"
				dir('vote'){
					sh 'pip install -r requirements.txt'
				}
			}
		}
		stage('test-vote') {
			when{
				changeset "**/vote/**"
			}
			agent {
				docker {
					image 'python:2.7.16-slim'
					args '--user root -v $HOME/.m2:/root/.m2'
				}
			}
			steps{
				echo "Running Unit Tests on vote app"
				dir('vote'){
					sh 'pip install -r requirements.txt'
					sh 'nosetests -v'
				}
			}
		}
		stage('docker-package-vote'){
			agent any
			when {
				branch 'master'
				changeset "**/vote/**"
			}
			steps{
				echo 'Packaging vote app with docker'
				script{
					docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
						def voteImage = docker.build("lbpaulino/vote:v${env.BUILD_ID}", "./vote")
						voteImage.push()
						voteImage.push("latest")
					}
				}
			}
		}

		stage('Sonarqube') {
			agent any
			when{
				branch 'master'
			}
			tools{
				jdk "JDK11" // the name you have given the JDK installation in Global Tool Configuration
			}
			environment{
				sonarpath = tool 'SonarScanner'
			}
			steps {
				echo 'Running Sonarqube Analysis..'
				withSonarQubeEnv('sonar-instavote') {
					sh "${sonarpath}/bin/sonar-scanner -Dproject.settings=sonar-project.properties -Dorg.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL=86400"
				}
			}
		}

		stage("Quality Gate") {
			steps {
				timeout(time: 1, unit: 'HOURS') {
					// Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
					// true = set pipeline to UNSTABLE, false = don't
					waitForQualityGate abortPipeline: true
				}
			}
		}

		stage('deploy to dev'){
			agent any
			when{
				branch 'master'
			}
			steps{
				echo 'Deploy instavote app with docker compose'
				sh 'docker-compose up -d'
			}
		}
	}
	post{
		always{
			echo 'Building multibranch pipeline for worker/vote/result is completed..'
		}
        	failure{
			slackSend(channel: "instavote-cd", message: "Build Failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
        	}
        	success{
			slackSend(channel: "instavote-cd", message: "Build Succeeded - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
        	}
	}
}
