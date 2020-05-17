pipeline{
    tools {
        maven "Maven 3.6.1"
    }
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v $HOME/.m2:/root/.m2'
        }
    }
    stages{
        stage("Build"){
            steps{
                echo "Compiling worker"
                dir("worker") {
                    sh "mvn compile";
                }
            }
        }
        stage("Test"){
            steps{
                echo "Testing worker"
                sleep 9;
            }
        }
        stage("Package"){
            steps{
                echo "Packaging worker"
                sleep 5;
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
    }
}