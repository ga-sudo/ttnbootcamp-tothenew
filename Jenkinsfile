pipeline {
    agent any 
    stages {
        stage('Docker Build') {
            steps {
                echo 'building docker'
		sh 'docker build -t 187632318301.dkr.ecr.us-east-1.amazonaws.com/ayush-ecr:$(git rev-parse HEAD) .'
            }
        }
        stage('pushing image to ECR') {
            steps {
                echo 'pushinfg image'
		sh 'docker push 187632318301.dkr.ecr.us-east-1.amazonaws.com/ayush-ecr:$(git rev-parse HEAD)'
            }
        }
        stage('Deploying on eks') {
            steps {
		script {
		     try {
                	echo 'deploying on eks'
			sh 'kubectl create -f deployment.yml'
			sh 'sleep 10'
			sh 'kubectl get pods'
			sh 'kubectl create -f loadbalancer.yml'
			sh 'sleep 5'
			sh 'kubectl get svc'
			sh 'kubectl describe pods'
			} catch (e) {
                        currentBuild.result = 'FAILURE'
                        slackHelper.buildGenericJobFailureNotificationMessage()
                        throw e
                    }
		} 
            }
        }
    }
    post {
        always {
            emailext body: 'A Test EMail', recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']], subject: 'Test'
        }
    }
}
