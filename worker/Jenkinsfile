pipeline {
    agent any
    tools {
        maven "Maven 3.6.3"
    }

    stages {
        stage('build') {
            steps {
                echo 'Compiling worker app'
                dir['worker'] {
                    sh 'mvn compile'
                }
            }
        }
        stage('test') {
            steps {
                echo 'Running tests'
            }
        }
        stage('package') {
            steps {
                echo 'Packaging worker app'
            }
        }
    }
    
    post {
        always{
            echo "Build pipeline completed"
        }
    }
}

