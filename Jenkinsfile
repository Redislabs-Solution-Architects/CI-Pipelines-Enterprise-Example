pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                script {
                    withCredentials(
			[usernamePassword(
				credentialsId: 'redis-cluster-creds',
				usernameVariable: 'REDIS_USERNAME',
			passwordVariable: 'REDIS_PASSWORD')]) {
                        sh 'env'
                     }
                }
            }
        }

        stage('Example stage 2') {
            steps {
                echo "Stage 2"
            }
        }
    }
}
