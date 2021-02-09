pipeline {
  agent any

  tools {
    maven 'Maven 3.6.1'
  }

  stages {
    stage('build') {
      echo 'Compiling the worker app'

      dir('worker') {
        sh 'mvn compile'
      }
    }

    stage('test') {
      echo 'Running unit tests'
    }

    stage('packaging') {
      echo 'Packaging the worker appl'
    }
  }
}
