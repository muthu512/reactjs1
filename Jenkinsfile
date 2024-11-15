pipeline {
    agent any

    environment {
        PROJECT_DIR = "C:\\Users\\Dell-Lap\\Downloads\\react-hello-world-master\\react-hello-world-master"
        TOMCAT_DIR = "C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\webapps"
        APP_NAME = "login"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs() // Cleans up the workspace
            }
        }

        stage('Checkout SCM') {
            steps {
                script {
                    git credentialsId: 'muthu512', url: 'https://github.com/muthu512/reactjs1', branch: 'master'
                    bat 'git fetch --all && git reset --hard origin/master'
                    bat 'git log -1' // Log the latest commit hash
                }
            }
        }

        stage('Check Node and npm Versions') {
            steps {
                script {
                    bat 'node -v || exit 1'
                    bat 'npm -v || exit 1'
                }
            }
        }

        stage('Check Environment Variables') {
            steps {
                script {
                    bat 'echo %PATH%'
                }
            }
        }

        stage('Prepare Project') {
            steps {
                script {
                    dir(PROJECT_DIR) {
                        bat 'if exist "package.json" (echo package.json exists) else (echo package.json not found && exit 1)'
                    }
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    dir(PROJECT_DIR) {
                        bat 'npm install'
                    }
                }
            }
        }

        stage('Optimized Build React App') {
            steps {
                script {
                    dir(PROJECT_DIR) {
                        // Build the React app
                        bat 'set NODE_OPTIONS=--openssl-legacy-provider && npm run build || exit 1'

                        // Validate build directory
                        bat "if exist build (echo Build directory exists) else (echo Build directory does not exist && exit 1)"
                        bat "dir \"${PROJECT_DIR}\\build\"" // Log build directory content
                    }
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    // Check if the build directory exists
                    bat """
                    if not exist "${env.PROJECT_DIR}\\build" (
                        echo Build directory does not exist && exit 1
                    )
                    if not exist "${env.PROJECT_DIR}\\build\\*" (
                        echo No files in build directory && exit 1
                    )
                    """

                    // Check and remove the old deployment directory
                    bat """
                    if exist "${env.TOMCAT_DIR}\\${env.APP_NAME}" (
                        rmdir /S /Q "${env.TOMCAT_DIR}\\${env.APP_NAME}"
                    )
                    """

                    // Copy new build to Tomcat
                    bat """
                    xcopy /E /I /Y "${env.PROJECT_DIR}\\build" "${env.TOMCAT_DIR}\\${env.APP_NAME}"
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'This will always run after the pipeline finishes'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        unstable {
            echo 'Pipeline unstable!'
        }
    }
}
