pipeline {

  agent none

  stages{
      stage('worker build'){
          agent {
            docker{
              image 'maven:3.6.1-jdk-8-alpine'
              args '-v $HOME/.m2:/root/.m2'
            }
          }
          when{
              changeset "**/worker/**"
          }
          steps{
              echo 'Compiling worker app'
              dir('worker'){
                sh 'mvn compile'
              }
          }
      }
      stage('worker test'){
          agent {
            docker{
              image 'maven:3.6.1-jdk-8-alpine'
              args '-v $HOME/.m2:/root/.m2'
            }
          }
          when{
              changeset "**/worker/**"
          }
          steps{
              echo 'Running Unit Tests on worker app'
              dir('worker'){
                sh 'mvn clean test'
              }
          }
      }
      stage('worker package'){
          agent {
            docker{
              image 'maven:3.6.1-jdk-8-alpine'
              args '-v $HOME/.m2:/root/.m2'
            }
          }
          when{
              branch 'master'
              changeset "**/worker/**"
          }
          steps{
              echo 'Packaging worker app'
              dir('worker'){
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
              }
          }
      }
      stage('worker-docker-package'){
          agent any
          when{
              changeset "**/worker/**"
              branch 'master'
          }
          steps{
              echo 'Packaging worker app with docker'
              script {
                docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  def workerImage = docker.build("serich1/worker:v${env.BUILD_ID}", "./worker")
                  workerImage.push()
                  workerImage.push("${env.BRANCH_NAME}")
                }
              }
          }
      }


      stage('result build'){
          agent{
            docker{
              image 'node:8.16.0-alpine'
            }
          }
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
          agent{
            docker{
              image 'node:8.16.0-alpine'
            }
          }
          when{
              changeset "**/result/**"
          }
          steps{
              echo 'Running Unit Tests on result app..'
              dir('result'){
                sh 'npm install'
                sh 'npm test'
              }
          }
      }
      stage('result-docker-package'){
          agent any
          when{
              changeset "**/result/**"
              branch 'master'
          }
          steps{
              echo 'Packaging result app with docker'
              script {
                docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                  def resultImage = docker.build("serich1/result:v${env.BUILD_ID}", "./result")
                  resultImage.push()
                  resultImage.push("${env.BRANCH_NAME}")
                }
              }
          }
      }


            stage('vote build'){
                agent{
                  docker{
                    image 'python:2.7.16-slim'
                    args '--user root'
                  }
                }
                when{
                    changeset "**/vote/**"
                }
                steps{
                    echo 'Compiling vote app..'
                    dir('vote'){
                      sh 'pip install -r requirements.txt'
                    }
                }
            }
            stage('vote test'){
                agent{
                  docker{
                    image 'python:2.7.16-slim'
                    args '--user root'
                  }
                }
                when{
                    changeset "**/vote/**"
                }
                steps{
                    echo 'Running Unit Tests on vote app..'
                    dir('vote'){
                      sh 'pip install -r requirements.txt'
                      sh 'nosetests -v'
                    }
                }
            }

            stage('vote-docker-package'){
                agent any
                when{
                    changeset "**/vote/**"
                    branch 'master'
                }
                steps{
                    echo 'Packaging vote app with docker'
                    script {
                      docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                        def voteImage = docker.build("serich1/vote:v${env.BUILD_ID}", "./vote")
                        voteImage.push()
                        voteImage.push("${env.BRANCH_NAME}")
                      }
                    }
                }
            }


  }

  post{
      always{
          echo 'Pipeline for instavote app is complete..'
      }
      failure{
          slackSend (channel: "instavote-cd", message: "Build failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
      }
      success{
          slackSend (channel: "instavote-cd", message: "Build succeeded - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)")
      }
  }

}
