pipeline {
    agent any

    environment {
        ECR_NAME = '618118007154.dkr.ecr.us-east-1.amazonaws.com/flaskapi'
        APP_REPO = 'https://github.com/lusoal/flask_api.git'
        K8S_REPO = 'https://github.com/lusoal/kubernetes-configs.git'
        DEPLOY_YAML = '03-flask-api.yaml'
    }

    stages {
        stage('Clonning Repository of my Application') {
            steps {
                sh 'git clone ${APP_REPO}'
            }
        }
        stage('Testing') {
            steps {
                sh 'sh cd flask_api && pytest test_class.py'
                //Executando teste unitario       
            }
        }
        stage('Build Application') {
            steps {
                sh 'cd flask_api && docker build -t ${ECR_NAME}:${BUILD_NUMBER} .'
                sh 'docker tag ${ECR_NAME}:${BUILD_NUMBER} ${ECR_NAME}:latest'
                
                //Login on ECR to push the image
                sh '$(aws ecr get-login --no-include-email)'
                
                sh 'docker push ${ECR_NAME}:${BUILD_NUMBER}'
                sh 'docker push ${ECR_NAME}:latest'
            }
        }
        stage ('Deployment on Kubernetes'){
            steps{
                sh 'git clone ${K8S_REPO}'
                sh 'cd kubernetes-configs/prd && sed -i s/__IMAGE__VALUE__/${BUILD_NUMBER}/g ${DEPLOY_YAML}'
                sh 'cd kubernetes-configs/prd && kubectl apply -f ${DEPLOY_YAML}'
            }

        }
    }
    post { 
            always { 
                cleanWs()
            }
        }

}