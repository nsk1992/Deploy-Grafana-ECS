pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        STACK_NAME = "GrafanaECSStack"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/nsk1992/Deploy-Grafana-ECS.git'
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
