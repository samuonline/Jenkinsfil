pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building the project.'
                // Use 'bat' instead of 'sh' for Windows
                sh 'mvn clean package'
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Running Unit and Integration Tests.'
                sh 'mvn test'
            }
            post {
                always {
                    emailext(
                        attachLog: true, // Attach the log to the email
                        to: 'dewumisamuduni@gmail.com',
                        subject: "Unit and Integration Tests: ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
                        body: "Unit and Integration Tests have been executed. Please check the results."
                    )
                }
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running Code Analysis.'
                sh 'mvn sonar:sonar' // Example command for code analysis
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying the application.'
                sh 'mvn deploy' // Example deploy command
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
