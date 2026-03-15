def PROJECT_REPO_URL = 'https://github.com/duonghieu0712z/kosmos.git'

pipeline {
    agent any

    trigger {
        githubPush()
    }

    parameters {
        gitParameter(
            name: 'BRANCH',
            type: 'PT_BRANCH',
            branchFilter: 'origin/(.*)',
            defaultValue: 'main',
            description: 'Select the branch to build',
            selectedValue: 'NONE',
            sortMode: 'ASCENDING_SMART',
            useRepository: PROJECT_REPO_URL,
            quickFilterEnabled: true,
            listSize: '5',
        )

        booleanParam 'BOOLEAN_VAR'
        reactiveChoice choiceType: 'PT_CHECKBOX', filterLength: 1, filterable: false, name: 'BOOLEAN_REACT', randomName: 'choice-parameter-24514683991800', referencedParameters: 'BOOLEAN_VAR', script: groovyScript(fallbackScript: [classpath: [], oldScript: '', sandbox: true, script: ''], script: [classpath: [], oldScript: '', sandbox: true, script: '''if (BOOLEAN_VAR == true) {
    return [\'IOS\', \'ANDROID\']
}'''])
        activeChoice choiceType: 'PT_CHECKBOX', filterLength: 1, filterable: false, name: 'CHOICE_VAR', randomName: 'choice-parameter-24514688876700', script: groovyScript(fallbackScript: [classpath: [], oldScript: '', sandbox: true, script: ''], script: [classpath: [], oldScript: '', sandbox: true, script: 'return [\'ENABLED\']'])
        reactiveChoice choiceType: 'PT_CHECKBOX', filterLength: 1, filterable: false, name: 'CHOICE_REACT', randomName: 'choice-parameter-24514691253300', referencedParameters: 'CHOICE_VAR', script: groovyScript(fallbackScript: [classpath: [], oldScript: '', sandbox: true, script: ''], script: [classpath: [], oldScript: '', sandbox: true, script: '''if (CHOICE_VAR == \'ENABLED\') {
    return [\'IOS\', \'ANDROID\']
}'''])
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
                    extensions: [[$class: 'CloneOption', noTags: false, reference: '', shallow: true, depth: 1, timeout: 4]],
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
