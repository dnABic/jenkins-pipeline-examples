

def userInput
try {
  userInput = input(
    id: 'Proceed1', message: 'Was this successful?', parameters: [
      [$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']
    ])
} catch(err) { // input false
  def user = err.getCauses()[0].getUser()
  userInput = false
  echo "Aborted by: [${user}]"
}
pipeline {
    agent any

    parameters {
        choice(choices: 'development\nstaging\nproduction', description: 'Environment parameter', name: 'env')
        booleanParam(name: 'buildAndDeploy',
          defaultValue: false,
          description: 'Make new builds and deploy them')
        string(name: 'topoVersion',
          description: 'Required topoentity version')
        string(name: 'jobMind',
          description: 'Required jobmind version')
    }
    stages {
        stage("Build") {
          steps {
            echo "building: topo version ${params.topoVersion}"
          }
        }
        stage("Continue") {
          steps {
            script {
              if (userInput == true) {
                echo "this was successful"
              } else {
                echo "this was not successful"
                currentBuild.result = 'FAILURE'
              }
            }
          }
        }
        stage("Approve Deployment") {
          steps {
            timeout(time: 1, unit: 'DAYS') {
              input message: 'Do you want to deploy?', submitter: 'ops'
            }
          }
        }
        stage("Deploy to production") {
          when {
            branch 'master'
          }
          steps {
            echo "flag: ${params.env}"
            sh("./deployment.py ${params.env} ${params.topoVersion}")
            sh("./deployment.py ${params.env} ${params.jobMind}")
          }
        }
        stage("Deploy to development") {
          when {
            anyOf {
              branch 'development'
            }
          }
          steps {
            echo "flag: ${params.env}"
            sh("./deployment.py ${params.env} ${params.topoVersion}")
            sh("./deployment.py ${params.env} ${params.jobMind}")
          }
        }
    }
}
