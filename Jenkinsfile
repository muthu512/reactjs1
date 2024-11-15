pipeline {
    agent any

    environment {
        PROJECT_DIR = "C:\\Users\\Dell-Lap\\Downloads\\react-hello-world-master\\react-hello-world-master"
        TOMCAT_DIR = "C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\work"
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
                    // Ensure build directory exists
                    bat "if not exist \"${PROJECT_DIR}\\build\" (echo Build directory does not exist && exit 1)"
                    bat "if not exist \"${PROJECT_DIR}\\build\\*\" (echo No files in build directory && exit 1)"

                    // Remove existing app from Tomcat
                    bat "rmdir /S /Q \"${TOMCAT_DIR}\\${APP_NAME}\""

                    // Deploy new app files
                    bat "mkdir \"${TOMCAT_DIR}\\${APP_NAME}\""
                    bat "xcopy /S /I /Y \"${PROJECT_DIR}\\build\\*\" \"${TOMCAT_DIR}\\${APP_NAME}\\\""

                    // Log deployed files
                    bat "dir \"${TOMCAT_DIR}\\${APP_NAME}\""

                    // Clear Tomcat cache and restart
                    bat "rmdir /S /Q \"C:\\Program Files\\Apache Software Foundation\\Tomcat 9.0\\work\""
                    bat "net stop Tomcat9"
                    bat "net start Tomcat9"
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
