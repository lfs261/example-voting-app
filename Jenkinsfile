pipeline{
    agent any

    tools{
        maven 'Maven 3.6.1'
    }
    
    stages{
        stage("build"){
            steps{
                echo "Building worker app..."
                dir("worker"){
                    sh 'mvn compile'
                }
            }
        }
        stage("test"){
            steps{
                echo "Testing worker app..."
                sleep 3
            }
        }
        stage("package"){
            steps{
                echo "Packaging worker app"
                sleep 7
            }
        }
        
    }
    post{
        always{
            echo " This pipeline run is complete."
        }
    }
    
}