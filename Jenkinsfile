pipeline {
    agent {
        docker {
            image 'node:lts-alpine'
        }
    }

    environment {
        AWS_CREDENTIAL = 'my_aws_credentials'
        AWS_REGION = 'eu-west-1'
        S3_BUCKET = 'corley-workshop-jenkins'
    }

    stages {
        stage("Install dependencies") {
            steps {
                script {
                    sh 'npm install'
                }
            }
        }

        stage("Test and publish result") {
            steps {
                script {
                    sh 'CI=true npm test -- --coverage'
                    step([$class: 'CoberturaPublisher', coberturaReportFile: 'coverage/cobertura-coverage.xml'])
                }
            }
        }

        stage("Build webapp") {
            steps {
                script {
                    sh 'npm run build'
                }
            }
        }

        stage("Publish on S3") {
            steps {
                withAWS(region: AWS_REGION, credentials: AWS_CREDENTIAL) {
                    s3Delete(bucket: S3_BUCKET, path:'**/*')
                    s3Upload(bucket: S3_BUCKET, workingDir:'build', includePathPattern:'**/*');
                }
            }
        }

    }
}
