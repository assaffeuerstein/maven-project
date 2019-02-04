pipeline {
    agent any

    triggers {
         pollSCM('* * * * *')
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Deploy in Parallel') {
            parallel{
                stage('Deploy to Staging') {
                    steps {
                        build job: 'Deploy-to-Staging'
                    }
                }
                stage('Deploy to Production') {
                    steps {
                        timeout(time:5, unit:'DAYS') {
                            input message: 'Approve PRODUCTION Deployment?'
                        }

                        build job: 'Deploy-to-Production'
                    }
                    post {
                        success {
                            echo 'Code deployed to Production'
                        }

                        failure {
                            echo 'Deployment failed'
                        }
                    }
                }
            }
        }
    }
}