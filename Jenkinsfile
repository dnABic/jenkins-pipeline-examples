pipeline {
    agent any

    parameters {
        booleanParam(name: 'buildAutoNames',
          defaultValue: false,
          description: 'Get new tags for applications automatically')
        string(name: 'topoVersion',
          description: 'Enter topoentity version')
        string(name: 'jobMind',
          description: 'Enter jobmind version')
    }
    stages {
        stage ('Build topoentity') {
          steps {
            echo "building: topoentity version ${params.topoVersion}"
            build job: 'topoentityBuild'
          }
        }
        stage ('Build jobmind') {
          steps {
            echo "building: jobmind version ${params.jobMind}"
            build job: 'jobmindBuild'
          }
        }
        stage("Deploy topoentity to staging") {
          when {
            branch 'master'
          }
          steps {
            sh("./deployment.py staging ${params.topoVersion}")
          }
        }
        stage("Deploy jobmind to staging") {
          when {
            branch 'master'
          }
          steps {
            sh("./deployment.py staging ${params.jobMind}")
          }
        }
        stage("Testing") {
          when {
            branch 'master'
          }
          steps {
            echo "Run some tests"
          }
        }
        stage("Approve Deployment") {
          steps {
            timeout(time: 1, unit: 'DAYS') {
              input message: 'Do you want to deploy?', submitter: 'admin'
            }
          }
        }
        stage("Deploy topoentity to production") {
          when {
            branch 'master'
          }
          steps {
            sh("./deployment.py prod ${params.topoVersion}")
          }
        }
        stage("Deploy jobmind to development") {
          when {
            branch 'master'
          }
          steps {
            sh("./deployment.py prod ${params.jobMind}")
          }
        }
    }
}
