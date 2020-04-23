pipeline {
    agent any
    tools{
        maven 'Maven 3.6.1'
    }
    stages{
        stage("build"){
            steps{
                echo 'Compiling worker app..'
                dir('worker'){
                    sh 'mvn compile'
                }
            }
        }
        stage("test"){
            steps{
                echo 'Running Unit Tets on worker app..'
                dir('worker'){
                    sh 'mvn clean test'
                }
            }
        }
        stage("package"){
            steps{
                echo 'Packaging worker app'
                dir('worker'){
                    sh 'mvn package'
                    archiveArtifacts artifacts: "**/target/*.jar"
                }
            }
        }
    }
    post{
        always{
            echo 'Building multibranch pipeline for worker is completed..'
        }
    }
}