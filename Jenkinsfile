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
                    sh '''
                        curl -X POST \
                          'https://ec2-18-140-203-30.ap-southeast-1.compute.amazonaws.com/ots/bridge/bridge/rest/services?overwrite=true&overwritePrefs=false&startup=true&preserveNodeModules=false&npmInstall=false&runScripts=false&stopTimeout=10&allowKill=false' \
                          -H 'accept: application/json' \
                          -H 'Content-Type: multipart/form-data' \
                          -F 'uploadFile=@IPSHealthCheckBN.rep'
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
