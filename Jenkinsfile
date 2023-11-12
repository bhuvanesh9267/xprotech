pipeline{
    agent any
    
    environment{
        aws_credentials = credentials('aws')
        aws_region = 'ap-south-1'
        aws_output = 'json'
    }

    stages{

        stage('aws configure'){
            steps{
                sh "aws configure set aws_access_key_id $aws_credentials_USR && aws configure set aws_secret_access_key $aws_credentials_PSW && aws configure set region $aws_region && aws configure set output $aws_output"
            }
        }
        
        stage('create ecr'){
            steps{
                sh "aws cloudformation create-stack --stack-name xprotechecr --template-body file://template.ecr.yaml --region 'ap-south-1'"
            }

        }

        stage('tag image'){
            steps{
                sh "docker tag xprotech:1.1 837016921632.dkr.ecr.ap-south-1.amazonaws.com/xprotech/ecr:1.1"
            }
        }

        stage('aws ecr configure'){
            steps{
                sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 837016921632.dkr.ecr.ap-south-1.amazonaws.com"
            }
        }

        stage('push image'){
            steps{
                sh "docker push 837016921632.dkr.ecr.ap-south-1.amazonaws.com/xprotech/ecr:1.1"
            }
        }

        stage('store image value to ssm'){
            steps{
                sh "aws ssm put-parameter --name /ecr/image --value 837016921632.dkr.ecr.ap-south-1.amazonaws.com/xprotech/ecr:1.1 --type String --region ap-south-1 --overwrite"
            }
        }

        stage('create ecs and container'){
            steps{
                sh "aws cloudformation create-stack --stack-name xprotechecs --template-body file://template.ecs.yaml --capabilities CAPABILITY_NAMED_IAM --region 'ap-south-1'"
            }
        }

    }
}