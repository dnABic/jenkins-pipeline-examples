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
        stage ('Starting ART job') {
          steps {
            build job: 'RunArtInTest', parameters: [[$class: 'StringParameterValue', name: 'systemname', value: systemname]]
          }
        }
        stage("Approve Deployment") {
          steps {
            timeout(time: 1, unit: 'DAYS') {
              input message: 'Do you want to deploy?', submitter: 'admin'
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
