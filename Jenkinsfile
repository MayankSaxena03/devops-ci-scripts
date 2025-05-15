pipeline {
    agent any

    environment {
        DISCORD_WEBHOOK_URL = credentials('discord-webhook')
        BUILD_DIR = "${env.HOME}/Downloads/Jenkins-Builds"
        TIMESTAMP = "${new Date().format('yyyyMMdd_HHmmss')}"
        COMPILE_LOG = "${env.HOME}/Downloads/Jenkins-Builds/compile_log_${new Date().format('yyyyMMdd_HHmmss')}.txt"
        RUN_LOG = "${env.HOME}/Downloads/Jenkins-Builds/run_log_${new Date().format('yyyyMMdd_HHmmss')}.txt"
    }

    triggers {
        pollSCM('H/3 * * * *') // Poll every 3 minutes
    }

    stages {
        stage('Notify Start') {
            steps {
                script {
                    def msg = [
                        content: "🚀 Jenkins build has started for *${env.JOB_NAME}* (Build #${env.BUILD_NUMBER})"
                    ]
                    def payload = groovy.json.JsonOutput.toJson(msg)
                    sh """
                        curl -H "Content-Type: application/json" \
                             -X POST \
                             -d '${payload}' \
                             ${DISCORD_WEBHOOK_URL}
                    """
                }
            }
        }
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MayankSaxena03/jenkins-test.git'
            }
        }

        stage('Compile C++') {
            steps {
                script {
                    sh """
                        mkdir -p "${BUILD_DIR}"
                        echo "Compiling C++ files..." > "${COMPILE_LOG}"
                    """
                    def srcFiles = sh(script: 'ls *.cpp || true', returnStdout: true).trim()
                    if (srcFiles) {
                        sh """
                            g++ -o output_exec *.cpp >> "${COMPILE_LOG}" 2>&1
                        """
                    } else {
                        error "No .cpp files found in the repo."
                    }
                }
            }
        }

        stage('Run Executable') {
            when {
                expression { fileExists('output_exec') }
            }
            steps {
                sh """
                    echo "Running executable..." > "${RUN_LOG}"
                    ./output_exec >> "${RUN_LOG}" 2>&1
                """
            }
        }

        stage('Notify Discord') {
            when {
                expression { fileExists('output_exec') }
            }
            steps {
                script {
                    def msg = [
                        content: "✅ Jenkins build and run completed.\n📦 Logs:\n• Compile: `${COMPILE_LOG}`\n• Run: `${RUN_LOG}`"
                    ]
                    def payload = groovy.json.JsonOutput.toJson(msg)
                    sh """
                        curl -H "Content-Type: application/json" \
                             -X POST \
                             -d '${payload}' \
                             ${DISCORD_WEBHOOK_URL}
                    """
                }
            }
        }
    }

    post {
        failure {
            script {
                def msg = [
                    content: "❌ Jenkins pipeline failed.\n📦 Check logs:\n• Compile: `${COMPILE_LOG}`\n• Run: `${RUN_LOG}`"
                ]
                def payload = groovy.json.JsonOutput.toJson(msg)
                sh """
                    curl -H "Content-Type: application/json" \
                         -X POST \
                         -d '${payload}' \
                         ${DISCORD_WEBHOOK_URL}
                """
            }
        }
    }
}
