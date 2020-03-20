pipeline {
    agent any

    environment {
        AWS_CREDENTIAL = 'my_aws_credentials'
        AWS_REGION = 'eu-west-1'
        S3_BUCKET = 'corley-workshop-jenkins'
    }

    stages {
        stage("Install dependencies") {
            agent {
                docker {
                    image 'node:lts-alpine'
                }
            }
            
            steps {
                script {
                    sh 'npm install'
                }
            }
        }

        stage("Test and publish result") {
            agent {
                docker {
                    image 'node:lts-alpine'
                }
            }
            
            steps {
                script {
                    sh 'CI=true npm test -- --coverage'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'coverage/cobertura-coverage.xml'])
                }
            }
        }

        stage("Build webapp") {
            agent {
                docker {
                    image 'node:lts-alpine'
                }
            }
            
            steps {               
                script {
                    sh 'npm run build'
                }
            }
        }

        stage("Publish on S3") {
            steps {
                sh "/usr/bin/aws s3 rm s3://$S3_BUCKET/ --recursive"
                sh "/usr/bin/aws s3 sync build/ s3://$S3_BUCKET/ --recursive"
            }
        }

    }
}
