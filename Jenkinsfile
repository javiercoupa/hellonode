#!groovy

// node {
//     def app

//     stage('Clone repository') {
//         /* Let's make sure we have the repository cloned to our workspace */

//         checkout scm
//     }

//     stage('Build image') {
//         /* This builds the actual image; synonymous to
//          * docker build on the command line */

//         app = docker.build("hellonode")
//     }

//     stage('Test image') {
//         /* Ideally, we would run a test framework against our image.
//          * For this example, we're using a Volkswagen-type approach ;-) */

//         app.inside {
//             sh 'echo "Tests passed"'
//         }
//     }

//     stage('Push image') {
//         /* Finally, we'll push the image with two tags:
//          * First, the incremental build number from Jenkins
//          * Second, the 'latest' tag.
//          * Pushing multiple tags is cheap, as all the layers are reused. */
//         docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
//             app.push("${env.BUILD_NUMBER}")
//             app.push("latest")
//         }
//     }
// }


// https://jenkins2.coupadev.com/job/QE/job/docker/configure

def bash(cmd) {
  sh("""#!/bin/bash --login
  set -x
  export PATH=$PATH:$HOME/ci-scripts/
  . $HOME/ci-scripts/common-jenkins-helpers.sh
  """ + cmd)
}

pipeline {

  agent { label 'e2e' }

  options {
    buildDiscarder(logRotator(numToKeepStr:'10'))
    timeout(time: 2, unit: 'HOURS')
    timestamps()
    ansiColor('xterm')
  }

  environment {
    COMPOSE_PROJECT_NAME = "${env.JOB_NAME}-${env.BUILD_ID}"
  }

  stages {

    stage('setup') {
      steps {
        echo "::: project: ${env.COMPOSE_PROJECT_NAME}"
        sh "echo '${env.COMPOSE_PROJECT_NAME}' > .env"

        //javiercoupa/hellonode/
      }
    }

    stage('update local devscripts') {
      steps {
        // node('ami-baker-control') {
        sh 'cd /home/jenkins/ci-scripts/; git checkout master ; git reset --hard; git pull --rebase origin master'
        // }
      }
    }

    stage('compose up!') {
      steps {
        sh 'docker-compose up'
      }
    }
  } // stages

  post {
    always {
      archive 'tmp/*.log'
      //junit 'build/reports/**/*.xml'
      sh "docker-compose down -v"
    }
    success {
      echo "::: success"
    }
    failure {
      echo "::: failure"
    }
  } // post
}
