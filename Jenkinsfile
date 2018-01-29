pipeline {
    agent any

    parameters {
        booleanParam(name: 'buildAutoNames',
          defaultValue: false,
          description: 'Get new tags for applications automatically')
        string(name: 'topoVersion',
          description: 'Enter topoentity version')
        string(name: 'jobmindVersion',
          description: 'Enter jobmind version')
    }
    stages {
        stage ('Build topoentity') {
          steps {
            echo "building: topoentity version ${params.topoVersion}"
            //build job: 'topoentityBuild', parameters: [[$class: 'StringParameterValue', name: 'version', value: ${params.topoVersion}]]
          }
        }
        stage ('Build jobmind') {
          steps {
            echo "building: jobmind version ${params.jobmindVersion}"
            build job: 'jobmind', parameters: [[$class: 'StringParameterValue', name: 'taskType', value: "build"], [$class: 'StringParameterValue', name: 'jobmindVersion', value: "${params.jobmindVersion}"]]
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
            echo "deploying: jobmind version ${params.jobmindVersion} to staging"
            build job: 'jobmind', parameters: [[$class: 'StringParameterValue', name: 'taskType', value: "deploy"],
                                               [$class: 'StringParameterValue', name: 'jobmindVersion', value: "${params.jobmindVersion}"],
                                               [$class: 'StringParameterValue', name: 'environment', value: "staging"]]
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
            echo "deploying: jobmind version ${params.jobmindVersion} to production"
            build job: 'jobmind', parameters: [[$class: 'StringParameterValue', name: 'taskType', value: "deploy"],
                                               [$class: 'StringParameterValue', name: 'jobmindVersion', value: "${params.jobmindVersion}"],
                                               [$class: 'StringParameterValue', name: 'environment', value: "prod"]]
          }
        }
    }
}
