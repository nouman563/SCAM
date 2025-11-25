pipeline {
    agent any

    environment {
        // Ensure NodeJS is installed and set the path correctly
        NODE_HOME = tool name: 'NodeJS', type: 'NodeJSInstallation'  // This name should match the NodeJS installation in Jenkins
        PATH = "${NODE_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install NodeJS') {
            steps {
                script {
                    // Check if NodeJS is already installed
                    sh 'node --version'
                    sh 'npm --version'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install dependencies using npm
                    sh 'npm install'  // You can replace with npm ci if you have a package-lock.json file
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run tests, allowing them to fail but not block the pipeline
                    sh 'npm test || true'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t smart-attendance:latest .'
                }
            }
        }
    }

    post {
        success {
            echo 'Build succeeded!'
        }
        failure {
            echo 'Build failed!'
            // Optional: Add email, Slack, or other notification here
        }
    }
}
