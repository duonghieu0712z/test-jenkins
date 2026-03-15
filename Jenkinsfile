def PROJECT_REPO_URL = 'https://github.com/duonghieu0712z/kosmos.git'

pipeline {
    agent any

    triggers {
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

        activeChoice(
            name: 'BUILD_LIB_COCOS',
            choiceType: 'PT_CHECKBOX',
            script: groovyScript(
                script: [sandbox: true, script: 'return ["ENABLED"]']
            )
        )
        reactiveChoice(
            name: 'BUILD_OPTIONS',
            choiceType: 'PT_CHECKBOX',
            referencedParameters: 'BUILD_LIB_COCOS',
            script: groovyScript(
                script: [
                    sandbox: true,
                    script: '''
                    if (BUILD_LIB_COCOS == "ENABLED") {
                        return ["IOS", "ANDROID"]
                    }
                    '''
                ]
            )
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
                checkout scmGit(
                    branches: [[name: "*/${params.BRANCH}"]],
                    userRemoteConfigs: [[url: PROJECT_REPO_URL]],
                    extensions: [
                        [$class: 'CloneOption', noTags: false, reference: '', shallow: true, depth: 1, timeout: 4],
                    ],
                )
            }
        }

        stage('Build') {
            steps {
                echo "Building branch: ${params.BRANCH}"
                script {
                    if (params.BUILD_LIB_COCOS.contains('ENABLED')) {
                        echo 'Building with Cocos library...'
                    } else {
                        echo 'Building without Cocos library...'
                    }

                    if (params.BUILD_OPTIONS.contains('IOS')) {
                        echo 'Building for iOS...'
                    }

                    if (params.BUILD_OPTIONS.contains('ANDROID')) {
                        echo 'Building for Android...'
                    }
                }
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
    dir("${WORKSPACE}@tmp") {
        deleteDir()
    }
    dir("${WORKSPACE}@script") {
        deleteDir()
    }
    dir("${WORKSPACE}@script@tmp") {
        deleteDir()
    }
}
