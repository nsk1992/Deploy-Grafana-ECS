pipeline {
    agent any

    environment {
        STACK_NAME = "GrafanaECSStack"
        AWS_REGION = "us-east-1"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/nsk1992/Deploy-Grafana-ECS.git'
            }
        }

        stage('Deploy CloudFormation') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding', 
                     credentialsId: 'aws-credentials-id', 
                     accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                     secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']
                ]) {
                    script {
                        sh """
                        export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                        export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                        aws cloudformation deploy \
                        --template-file cloudformation.yaml \
                        --stack-name $STACK_NAME \
                        --capabilities CAPABILITY_NAMED_IAM
                        """
                    }
                }
            }
        }

        stage('Start ECS Service') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding', 
                     credentialsId: 'aws-credentials-id', 
                     accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                     secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']
                ]) {
                    script {
                        sh """
                        export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                        export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                        echo '{ "action": "start" }' | aws lambda invoke \
                        --function-name StartStopGrafanaECS \
                       --payload '{"action": "start"}' \
                       --cli-binary-format raw-in-base64-out \
                        response.json
                        """
                    }
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
