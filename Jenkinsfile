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

        stage('Deploy') {
            steps {
                dir('repository') {
                    bat '''
                        e2ebridge deploy repository/IPSHealthCheckBN.rep -h ec2-52-74-183-0.ap-southeast-1.compute.amazonaws.com -u ccasin -P Asdf!234 -o overwrite --port 8080
                    '''
                }
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
