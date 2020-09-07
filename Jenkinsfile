pipeline {
    agent any
    
    tools{
        maven '3.6.1'
    }

    stages{
        stage('build'){
            steps{
                echo 'Compile'
                dir('worker'){
                    sh 'mvn compile'
                }
            }
        }
        stage('test'){
            steps{
                echo 'Unit Test'              
            }
        }
        stage('package'){
            steps{
                echo 'Packaging'                
            }
        }
    }
    post{
        always{
            echo 'Complete!'
        }
    }
}