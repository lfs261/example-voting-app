pipeline {
  
  agent none
  
  stages{

      stage("worker build"){

        when{

            changeset "**/worker/**"

          }



        agent{

          docker{

            image 'maven:3.6.1-jdk-8-slim'

            args '-v $HOME/.m2:/root/.m2'

          }

        }



        steps{

          echo 'Compiling worker app..'

          dir('worker'){

            sh 'mvn compile'

          }

        }

      }

      stage("worker test"){

        when{

          changeset "**/worker/**"

        }

        agent{

          docker{

            image 'maven:3.6.1-jdk-8-slim'

            args '-v $HOME/.m2:/root/.m2'

          }

        }

        steps{

          echo 'Running Unit Tets on worker app..'

          dir('worker'){

            sh 'mvn clean test'

           }



          }

      }

      stage("worker package"){

        when{

          branch 'master'

          changeset "**/worker/**"

        }

        agent{

          docker{

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



      stage('worker docker-package'){

          agent any

          when{

            changeset "**/worker/**"

            branch 'master'

          }

          steps{

            echo 'Packaging worker app with docker'

            script{

              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {

                  def workerImage = docker.build("jeroenvanharten/worker:v${env.BUILD_ID}", "./worker")

                  workerImage.push()

                  workerImage.push("${env.BRANCH_NAME}")

                  workerImage.push("latest")

              }

            }

          }

      }
      stage('result build'){
            when{
                changeset "**/result/**"
              }
        steps{
          echo 'Compiling result app..'
          dir('result'){
            sh 'npm install'
          }
        }
    }
    stage('result test'){
      when{
        changeset "**/result/**"
      }
      steps{
        echo 'Running Unit Tets on result app..'
        dir('result'){
          sh 'npm install'
          sh 'npm test' 
          }
        }
    }
    }
  post{

    always{
        echo 'Building multibranch pipeline for instavote is completed..'
    }
    success {

           slackSend (channel: "instavote", message: "Build Succes - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")

       }

    failure {

           slackSend (channel: "instavote", message: "Build Failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")

      }
  }

}
