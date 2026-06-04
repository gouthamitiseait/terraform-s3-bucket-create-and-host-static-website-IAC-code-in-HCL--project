pipeline {
    agent any
    stages {
        stage ('pull code from github') {
            steps {
                git branch: 'master', url: 'https://github.com/gouthamitiseait/terraform-s3-bucket-create-and-host-static-website-IAC-code-in-HCL--project'
            }
        }
   
        stage ('terraform apply & init') {
            steps {
                withAWS(credentials: 'aws-credentials-gouthami', region: 'us-east-1') {
                    sh 'terraform init'
                    sh 'terraform validate'
                    sh 'terraform apply -auto-approve'
                }
            }
        }
        
        stage ('upload files to s3 bucket') {
            steps {
                withAWS(credentials: 'aws-credentials-gouthami', region: 'us-east-1') {
                    sh '''
                        BUCKET_NAME=$(terraform output -raw name | cut -d'.' -f1)
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
            echo 'static website deployment successful'
            sh 'terraform output -raw name'
        }
        failure {
            echo 'static website deployment failure'
        }
    }
}
