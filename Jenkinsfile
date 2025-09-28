pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        S3_BUCKET          = 'sushanth1107'   // replace with actual bucket
        CLOUDFRONT_ID      = 'E3CJ0CF4NBL60R'        // replace with your distribution ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/sushanth-max/frontend-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to S3') {
            steps {
                withAWS(credentials: 'c421e99a-cac2-4311-afb2-b32c8d11546d', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        aws s3 sync build/ s3://$S3_BUCKET --delete
                    '''
                }
            }
        }

        stage('Invalidate CloudFront Cache') {
            steps {
                withAWS(credentials: 'c421e99a-cac2-4311-afb2-b32c8d11546d', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        aws cloudfront create-invalidation \
                        --distribution-id $CLOUDFRONT_ID \
                        --paths "/*"
                    '''
                }
            }
        }
    }
}
