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
        stage("Deploy to production") {
          when {
            anyOf {
              branch: 'master'
            }
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
              branch: 'development'
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
