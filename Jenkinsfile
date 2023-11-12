pipeline{
    agent any
    stages{
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