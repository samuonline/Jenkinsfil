pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building the code...'
                bat 'mvn clean install'
            }
        }
        stage('Unit and Integration Tests') {
            steps {
                echo 'Running unit and integration tests...'
                bat 'mvn test'
            }
        }
        stage('Code Analysis') {
            steps {
                echo 'Performing code analysis...'
                bat 'sonar-scanner'
            }
        }
        stage('Security Scan') {
            steps {
                echo 'Running security scan...'
                bat 'dependency-check'
            }
        }
        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging...'
                bat 'aws deploy push ...'  // Replace ... with actual command
            }
        }
        stage('Integration Tests on Staging') {
            steps {
                echo 'Running integration tests on staging...'
                bat 'postman run tests'  // Ensure Postman CLI is correctly set up
            }
        }
        stage('Deploy to Production') {
            steps {
                echo 'Deploying to production...'
                bat 'aws deploy push ...'  // Replace ... with actual command
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            mail to: 'dewumisamuduni@gmail.com',
                 subject: "Pipeline Success",
                 body: """The pipeline has succeeded successfully. Check the logs for more details."""
        }
        failure {
            mail to: 'dewumisamuduni@gmail.com',
                 subject: "Pipeline Failure",
                 body: """The pipeline has failed. Please check the logs."""
        }
    }
}
