pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    triggers {
        pollSCM('H/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                    python3 -m venv .venv
                    .venv/bin/pip install --upgrade pip
                    .venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '.venv/bin/pytest -q'
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh 'sudo /usr/local/bin/deploy-flask-staging'
            }
        }
    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully.'
	    emailext(
                to: 'pardeshi.hitesh@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The Jenkins pipeline succeeded.\nJob: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}\nConsole: ${env.BUILD_URL}"
            )
        }
        failure {
            echo 'CI/CD pipeline failed. Check the stage logs.'
            emailext(
                to: 'pardeshi.hitesh@gmail.com',
                subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The Jenkins pipeline failed.\nJob: ${env.JOB_NAME}\nBuild: ${env.BUILD_NUMBER}\nPlease review the console output: ${env.BUILD_URL}console"
            )
        }
    }
}
