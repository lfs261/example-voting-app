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
                    sh "mvn compile -DskpTests";
                }
            }
        }
        stage("Test"){
            steps{
                echo "Testing worker"
                dir("worker") {
                    sh "mvn test";
                }
            }
        }
        stage("Package"){
            steps{
                echo "Packaging worker"
                dir("worker") {
                    sh "mvn package -DskipTests";
                }
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
    }
}