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
            listSize: '5'
        )

        activeChoice(
            name: 'BUILD_LIB_COCOS',
            choiceType: 'PT_CHECKBOX',
            script: groovyScript(
                script: [sandbox: true, script: 'return ["ENABLED"]']
            )
        )

        reactiveChoice(
            name: 'COCOS_VERSION',
            choiceType: 'PT_SINGLE_SELECT',
            referencedParameters: 'BUILD_LIB_COCOS',
            script: groovyScript(
                script: [
                    sandbox: true,
                    script: '''
                    if (BUILD_LIB_COCOS == "ENABLED") {
                        return ["v213", "v373"]
                    }
                    '''
                ]
            )
        )

        booleanParam(
            name: 'BUILD_IOS',
            defaultValue: false
        )

        booleanParam(
            name: 'BUILD_ANDROID',
            defaultValue: false
        )

        // reactiveChoice(
        //     name: 'BUILD_OPTIONS',
        //     choiceType: 'PT_CHECKBOX',
        //     referencedParameters: 'BUILD_LIB_COCOS',
        //     script: groovyScript(
        //         script: [
        //             sandbox: true,
        //             script: '''
        //             if (BUILD_LIB_COCOS == "ENABLED") {
        //                 return ["IOS", "ANDROID"]
        //             }
        //             '''
        //         ]
        //     )
        // )

        activeChoiceHtml(
            name: 'BUILD_OPTIONS',
            choiceType: 'ET_FORMATTED_HTML',
            referencedParameters: 'BUILD_LIB_COCOS',
            script: groovyScript(
                script: [
                    sandbox: true,
                    script: '''
                    def cocosVersions = ['v213', 'v373']
                    def buildPlatforms = ['BUILD_IOS', 'BUILD_ANDROID']

                    StringBuilder html = new StringBuilder()

                    html.append('<div style="padding: 10px;">')

                    if (BUILD_LIB_COCOS == 'ENABLED') {
                        html.append('<label style="font-weight: bold;">COCOS_VERSION:</label><br/>')
                        html.append('<select name="value" style="margin-bottom: 10px; padding: 5px;">')
                        cocosVersions.each { ver ->
                            html.append("<option value='${ver}'>${ver}</option>")
                        }
                        html.append('</select><br/>')
                    } else {
                        html.append('<input type="checkbox" name="value" value="true">USE_EMBED_CORE<br/>')
                    }

                    buildPlatforms.each { plat ->
                        html.append('<span class = "jenkins-checkbox">')
                        html.append("<input type='checkbox' name='value' class='  '")
                        html.append("><label class='attach-previous '>${plat}</label>")
                        html.append('</span>')
                    }

                    html.append('</div>')

                    return html.toString()
                    '''
                ]
            )
        )

        booleanParam(
            name: 'IS_BUILD_RELEASE',
            defaultValue: false
        )

        booleanParam(
            name: 'SEND_NOTIFICATION',
            defaultValue: true
        )

        string(
            name: 'RELEASE_NOTES',
            description: 'Release notes',
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
                    extensions: [
                        [$class: 'CloneOption', noTags: false, reference: '', shallow: true, depth: 1, timeout: 4],
                    ]
                ])
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

                    echo "Build options: ${params.BUILD_OPTIONS}"

                    if (params.IS_BUILD_RELEASE) {
                        echo 'Building release...'
                    } else {
                        echo 'Building debug...'
                    }

                    if (params.SEND_NOTIFICATION) {
                        echo 'Sending notification...'
                    }

                    if (params.RELEASE_NOTES) {
                        echo "Release notes: ${params.RELEASE_NOTES}"
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
