pipeline {
    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/vikasch123/Terraform-Jenkins.git'
                    sh 'mkdir -p terraform'
                    sh 'rsync -av --exclude=.git --exclude=terraform . terraform/'
                }
            }
        }

        stage('Plan') {
            steps {
                sh '''
                cd terraform
                terraform init
                terraform plan -out tfplan
                terraform show -no-color tfplan > tfplan.txt
                '''
            }
        }

        stage('Approval') {
            when {
                not {
                    equals expected: true, actual: params.autoApprove
                }
            }
            steps {
                script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                }
            }
        }

        stage('Apply') {
            steps {
                sh '''
                cd terraform
                terraform apply -input=false tfplan
                '''
            }
        }
    }
}
