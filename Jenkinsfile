def PROJECT_REPO_URL = 'https://github.com/duonghieu0712z/kosmos.git'

pipeline {
    agent any

    parameters {
        gitParameter(
            name: 'BRANCH',
            type: 'PT_BRANCH',
            branchFilter: 'origin/(.*)',
            defaultValue: 'main',
            description: 'Select the branch to build',
            selectedValue: 'none',
            sortMode: 'ASCENDING_SMART',
            useRepository: PROJECT_REPO_URL,
            quickFilter: true,
            listSize: '5',
        )
    }

    stages {
        stage('Clean') {
            steps {
                cleanws()
            }
        }

        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/${params.BRANCH}"]],
                    userRemoteConfigs: [[url: PROJECT_REPO_URL]],
                ])
            }
        }
    }

    post {
        always {
            cleanws()
        }
    }
}

def cleanws() {
    cleanWs()
}
