pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                url: 'https://github.com/gouthamitiseait/terraform-s3-bucket-create-and-host-static-website-IAC-code-in-HCL--project'
            }
        }

        stage('Terraform Init & Apply') {
            steps {
                withAWS(credentials: 'aws-creds-id', region: 'ap-south-1') {
                    sh '''
                        terraform init
                        terraform validate
                        terraform apply -auto-approve
                    '''
                }
            }
        }

        stage('Upload Files to S3') {
            steps {
                withAWS(credentials: 'aws-creds-id', region: 'ap-south-1') {
                    sh '''
                        BUCKET_NAME=$(terraform output -raw name)

                        echo "Uploading to bucket: $BUCKET_NAME"

                        aws s3 sync ./ s3://$BUCKET_NAME \
                          --exclude ".git/*" \
                          --exclude ".terraform/*" \
                          --exclude "terraform.lock.hcl" \
                          --exclude "*.tf" \
                          --exclude "*.hcl" \
                          --exclude "Jenkinsfile" \
                          --exclude "*.md"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Static website deployed successfully'
            sh 'terraform output'
        }

        failure {
            echo '❌ Static website deployment failed'
        }
    }
}
