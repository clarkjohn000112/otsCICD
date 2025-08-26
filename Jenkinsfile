#!groovy

pipeline {
    agent {
        node {
            label 'Windows'
            customWorkspace "workspace/test-xUML-project"
        }
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '1'))
        disableConcurrentBuilds()
    }
    
    parameters {
        choice(name: 'XUMLC', choices: 'D:/jenkins/userContent/xumlc/xumlc-7.10.0.jar', description: 'Location of the xUML Compiler')
        choice(name: 'REGTEST', choices: 'D:/jenkins/userContent/RegTestRunner/RegTestRunner-nightly.jar', description: 'Location of the Regression Test Runner')
    }

    stages {
        stage('Build') {
            steps {
                dir('Advanced Modeling/E2ELibrary') {
                    bat """
                        java -jar ${XUMLC} -uml uml/librarySQLQuery.xml
                        copy repository\\librarySQLQuery\\librarySQLQuery.lrep libs\\
                        java -jar ${XUMLC} -uml uml/useLibrarySQLQuery.xml
                    """
                    archiveArtifacts artifacts: 'repository/useLibrarySQLQuery/UseE2ELibraryExample.rep'
                }
                
                dir('Advanced Modeling/PState') {
                    bat """
                        java -jar ${XUMLC} -uml uml/pstatePurchaseOrder.xml
                    """
                    archiveArtifacts artifacts: 'repository/pstatePurchaseOrder/PurchaseOrderExample.rep'
                }
            }
        }
        stage('Deploy') {
            steps {
                dir('Advanced Modeling') {
                    bat '''
                        e2ebridge deploy E2ELibrary/repository/useLibrarySQLQuery/UseE2ELibraryExample.rep -h <Bridge host> -u <user> -P <password> -o overwrite
                        e2ebridge deploy PState/repository/pstatePurchaseOrder/PurchaseOrderExample.rep -h <Bridge host> -u <user> -P <password> -o overwrite
                    '''
                }
            }
        }
        stage('Test') {
            steps {
                dir('Advanced Modeling') {
                    bat """
                        java -jar ${REGTEST} -project PState -suite "QA Tests/Tests" -logfile result.xml -host <Bridge host> -port <port> -username <user> -password <password>
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
