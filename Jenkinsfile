pipeline {
   agent any
   tools{
	maven 'Maven 3.6.3'
   }
   
   stages {
        stage('build') {
            steps {
                echo 'compiling Worker App'
                dir('worker'){
		   sh 'mvn compile'
		}
            }
        }
        stage('test') {
            steps {
                echo 'Running unit tests on worker App'
            }
        }
        stage('package') {
            steps {
                echo 'Packaging Worker App'
            }
        }
    }
    post {
        always {
            echo 'the pipeline step is completed succesfully'
        }
    }
}
