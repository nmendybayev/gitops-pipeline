pipeline{
    agent{
        label "jenkins-agent"
    }

    environment {
        APP_NAME = "enterprise-cicd"
    }

    stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'github-pipe', url: 'https://github.com/nmendybayev/gitops-pipeline'
            }

        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    cat deployment.yaml
                    sed -i 's/${APP_NAME}-.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                    cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "NurbolM"
                    git config --global user.email "nmendybaev@gmail.com"
                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest"
                """
                withCredentials([usernamePassword(credentialsId: 'github-pipe', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh "git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/nmendybayev/gitops-pipeline main"
                }
            }
        }
    }
}     