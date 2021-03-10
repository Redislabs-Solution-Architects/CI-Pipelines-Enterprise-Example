pipeline {

    agent any

    options {
        disableConcurrentBuilds()
    }

    stages {
        stage("startup container"){
            steps{
                sh 'docker run --rm -d --cap-add sys_resource --name redisenterprise -p 8443:8443 -p 9443:9443 -p 12000:12000 redislabs/redis:latest'
		echo "Waiting for cluster to spin up"
                sh 'sleep 30'
            }
        }
        stage("build cluster"){
            steps{
		echo "Setting up the cluster"
                sh 'docker exec -d --privileged redisenterprise "/opt/redislabs/bin/rladmin" cluster create name cluster.local username cihan@redislabs.com password redislabs123'
                sh 'sleep 10'
            }
        }
        stage("create database"){
            steps{
		echo "Creating a database"
                sh "curl -sk -u cihan@redislabs.com:redislabs123 --request POST --url https://localhost:9443/v1/bdbs --header 'content-type: application/json' --data '{\"name\":\"db1\",\"type\":\"redis\",\"memory_size\":102400,\"port\":12000}'"
            }

        }  
        stage("ping database"){
            steps{
                sh 'sleep 10'
                sh 'redis-cli -p 12000 PING'
            }
        }  
    }
    post {
        always {
            echo "Cleaning up the container"
            sh 'docker stop redisenterprise'
        }
    }    
}
