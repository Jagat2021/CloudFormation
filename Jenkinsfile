pipeline{
    agent{
        label "SlaveNode"
    }
    stages{
        stage("Git Clone") {
            steps{
                git branch: 'main', credentialsId: '33e25b3f-78dc-4097-bc7d-76045b9fe7e3', url: 'https://github.com/Jagat2021/CloudFormation.git'
            }

        }
        stage("CF-Validate"){
            steps{
                sh 'aws cloudformation validate-template --template-body file://$WORKSPACE/one_base_template.yml'
            }
            post {
                success {
                 archiveArtifacts artifacts: '*.yml', followSymlinks: false
                }
            }


        }
        stage("CF-CreateStack"){
            steps{
                sh 'aws cloudformation create-stack --stack-name myteststack --template-body file://$WORKSPACE/one_base_template.yml'
            }

        }
        stage("Copy to S3"){
            steps{
                git branch: 'main', credentialsId: '33e25b3f-78dc-4097-bc7d-76045b9fe7e3', url: 'https://github.com/Jagat2021/CloudFormation.git'
                sh 'aws s3 cp one_base_template.yml s3://testbucket-jagat/one_base_template.yml'
            }
           

        }
    }

}