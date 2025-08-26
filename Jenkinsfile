pipeline {
    agent any

    triggers {
        // Run pipeline every time you push to GitHub
        pollSCM('H/5 * * * *')  // checks every 5 mins (or use webhook)
    }

    stages {
        stage('Checkout') {
            steps {
                // Jenkins automatically clones repo when using "Pipeline from SCM"
                echo "Repo checked out successfully."
            }
        }

        stage('List files') {
            steps {
                sh 'ls -R'
            }
        }

        stage('Run Test Command') {
            steps {
                // Example running your jar if needed
                sh 'java -jar tools/xumlc-7.20.0.jar -h || true'
            }
        }
    }
}
