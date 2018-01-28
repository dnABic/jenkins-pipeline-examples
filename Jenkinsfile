pipeline {
    agent any

    parameters {
        choice(choices: 'staging\nproduction', description: 'Environment parameter', name: 'env')
    }

    stages {
        stage("deployment") {
            steps {
                echo "flag: ${params.env}"
            }
        }
    }
}
