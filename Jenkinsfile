pipeline {
    agent any

    environment {
        STACK_NAME = "GrafanaECSStack"
        AWS_ACCESS_KEY_ID = credentials('your-aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('your-aws-secret-access-key')
        AWS_DEFAULT_REGION = 'us-east-1
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url:'https://github.com/nsk1992/Deploy-Grafana-ECS.git'
            }
        }

        stage('Deploy CloudFormation') {
            steps {
                script {
                    sh """
                    aws cloudformation deploy \
                    --template-file cloudformation.yaml \
                    --stack-name $STACK_NAME \
                    --capabilities CAPABILITY_NAMED_IAM
                    """
                }
            }
        }

        stage('Start ECS Service') {
            steps {
                script {
                    sh """
                    aws lambda invoke \
                    --function-name StartStopGrafanaECS \
                    --payload '{ "action": "start" }' \
                    response.json
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
