#!groovy

pipeline {
    agent any   // run on any Linux agent

    options {
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '1'))
        disableConcurrentBuilds()
    }
    
    parameters {
        choice(
            name: 'XUMLC', 
            choices: '/root/jenkins-cd/tools/xumlc-7.20.0.jar', 
            description: 'Location of the xUML Compiler'
        )
        choice(
            name: 'REGTEST', 
            choices: '/root/jenkins-cd/tools/RegTestRunner-nightly.jar', 
            description: 'Location of the Regression Test Runner'
        )
        choice(
            name: 'E2EBRIDGE',
            choices: '/root/jenkins-cd/tools/e2ebridge',
            description: 'Location of the Bridge CLI'
        )
    }

    stages {
        stage('Build') {
            steps {
                dir('Advanced Modeling/E2ELibrary') {
                    sh """
                        java -jar ${params.XUMLC} -uml uml/librarySQLQuery.xml
                        cp repository/librarySQLQuery/librarySQLQuery.lrep libs/
                        java -jar ${params.XUMLC} -uml uml/useLibrarySQLQuery.xml
                    """
                    archiveArtifacts artifacts: 'repository/useLibrarySQLQuery/UseE2ELibraryExample.rep'
                }
                
                dir('Advanced Modeling/PState') {
                    sh """
                        java -jar ${params.XUMLC} -uml uml/pstatePurchaseOrder.xml
                    """
                    archiveArtifacts artifacts: 'repository/pstatePurchaseOrder/PurchaseOrderExample.rep'
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('Advanced Modeling') {
                    sh """
                        ${params.E2EBRIDGE} deploy E2ELibrary/repository/useLibrarySQLQuery/UseE2ELibraryExample.rep \
                          -h ec2-18-140-203-30.ap-southeast-1.compute.amazonaws.com \
                          -u ccasin -P 'Asdf!234' -o overwrite

                        ${params.E2EBRIDGE} deploy PState/repository/pstatePurchaseOrder/PurchaseOrderExample.rep \
                          -h ec2-18-140-203-30.ap-southeast-1.compute.amazonaws.com \
                          -u ccasin -P 'Asdf!234' -o overwrite
                    """
                }
            }
        }

        stage('Test') {
            steps {
                dir('Advanced Modeling') {
                    sh """
                        java -jar ${params.REGTEST} \
                          -project PState \
                          -suite "QA Tests/Tests" \
                          -logfile result.xml \
                          -host ec2-18-140-203-30.ap-southeast-1.compute.amazonaws.com \
                          -port 8080 \
                          -username ccasin \
                          -password 'Asdf!234'
                    """
                }
            }
            post {
                always {
                    junit 'Advanced Modeling/result.xml'
                }
            }
        }
    }
}
