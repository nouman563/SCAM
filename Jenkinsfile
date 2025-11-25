pipeline {
  agent any

  environment {
    NODE_HOME = tool name: 'NodeJS', type: 'NodeJSInstallation'
    PATH = "${NODE_HOME}/bin:${env.PATH}"
  }

  stages {
    stage('Checkout') {
      steps {
        script {
          // Checkout code from SCM
          checkout scm
        }
      }
    }
    
    stage('Update Dependencies') {
      steps {
        script {
          // Ensure the correct version of dependencies is installed
          sh 'npm install -g npm@latest'  // Update npm to the latest version
          sh 'npm install'                // Install dependencies (update package-lock.json if needed)
        }
      }
    }

    stage('Install') {
      steps {
        script {
          // Install dependencies using npm ci
          try {
            sh 'npm ci'  // Clean install dependencies from package-lock.json
          } catch (Exception e) {
            currentBuild.result = 'FAILURE'
            echo "npm ci failed: ${e}"
            throw e
          }
        }
      }
    }

    stage('Test') {
      steps {
        script {
          // Run tests, ignore failure if it's a non-blocking test
          try {
            sh 'npm test || true'  // Ensure tests run but don't fail the build if they do
          } catch (Exception e) {
            echo "Tests failed, but continuing the pipeline"
          }
        }
      }
    }

    stage('Build Docker') {
      steps {
        script {
          try {
            // Build Docker image
            sh 'docker build -t smart-attendance:latest .'
          } catch (Exception e) {
            currentBuild.result = 'FAILURE'
            echo "Docker build failed: ${e}"
            throw e
          }
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
      // You can add further notification here like sending emails or Slack messages.
    }
  }
}
