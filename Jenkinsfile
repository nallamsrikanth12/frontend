pipeline {
    agent {
        label 'Agent-1'
    }
    options {
        // Timeout counter starts BEFORE agent is allocated
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    environment {
        def appversion = '' // variable declaration
        nexusUrl = 'nexus.srikantheswar.online:8081'
    }

    stages {
        stage('read the json files') {
            steps {
                script {
                    def packagejson = readJSON file: 'package.json'
                    appversion = packagejson.version
                    echo "application version : ${appversion}"
                }
            }
        }
        stage('Build') {
            steps {
                sh """
                    zip -q -r frontend.${appversion}.zip * -x jenkinsfile -x frontend.${appversion}.zip
                    ls -ltr
                """
            }
        }
        stage('Nexus artifacts uploader') {
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appversion}",
                        repository: 'frontend',
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [
                                artifactId: "frontend",
                                classifier: '',
                                file: "frontend.${appversion}.zip",
                                type: 'zip'
                            ]
                        ]
                    )
                }
            }
        }
        stage ('trigger the frontend-deploy') {
            steps {
                script {
                    def params = [
                    string(name: 'appversion', value: "${appversion}")
                ]
                    build job: 'frontend-deployment', parameters: params, wait: false
}
        }

                }
            }
        
    
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'I will run pipeline is success'
        }
        failure { 
            echo 'I will run pipeline is failure'
        }
    }
}
