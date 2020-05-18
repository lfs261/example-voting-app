pipeline{
    // tools {
    //     maven "Maven 3.6.1"
    // }
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v $HOME/.m2:/root/.m2'
        }
    }
    stages{
        stage("Build"){
            when{
                changeset "**/worker/**"
            }
            steps{
                echo "Compiling worker"
                dir("worker") {
                    sh "mvn compile -DskpTests";
                }
            }
        }
        stage("Test"){
            when{
                changeset "**/worker/**"
            }
            steps{
                echo "Testing worker"
                dir("worker") {
                    sh "mvn clean test";
                }
            }
        }
        stage("Package"){
            when{
                branch "master"
                changeset "**/worker/**"
            }
            steps{
                echo "Packaging worker"
                dir("worker") {
                    sh "mvn package -DskipTests";
                    archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                }
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            slackSend(channel:"devops",color: "#15A339", message:"*Build Success*  \n\n Job Name : ${env.JOB_BASE_NAME} \n Build Number: ${env.BUILD_NUMBER} \n Branch: ${env.BRANCH_NAME} \n Build URL: (<${env.BUILD_URL} | Open>)")
        }
        failure{
            slackSend(channel:"devops",color: "#F00A21", message:"<b>Build Success</b> \n\n Job Name : ${env.JOB_BASE_NAME} \n Build Number: ${env.BUILD_NUMBER} \n Branch: ${env.BRANCH_NAME} \n Build URL: (<${env.BUILD_URL} | Open>)")
        }
    }
}