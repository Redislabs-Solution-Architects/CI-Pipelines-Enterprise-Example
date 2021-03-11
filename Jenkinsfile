pipeline {
    agent any
    stages {
        stage('Create Remote Databse') {
            steps {
                script {
                    withCredentials(
			[usernamePassword(
				credentialsId: 'redis-cluster-creds',
				usernameVariable: 'REDIS_USERNAME',
			passwordVariable: 'REDIS_PASSWORD')]) {
                	sh '/mnt/host_machine/tools/mkdb-byname.py --redis-host ${params.cluster} --password $REDIS_PASSWORD --username $REDIS_USERNAME --db-name jenkins${BUILD_NUMBER} --memory-size 107374182 --shard-count 2 --db-password jenkinstest --replication --db-port 12113'
                     }
                }
            }
        }
        stage('Wait for DB to activate') {
            steps {
		sh 'sleep 20'
            }
        }
        stage('Collect DB Redis Info') {
            steps {
		sh 'redis-cli -h redis-12113.${cluster} -p 12113 -a jenkinstest INFO SERVER'
            }
        }
        stage('Run Memtier Benchmark') {
            steps {
		sh 'memtier_benchmark --ratio=1:4 --test-time=60 -d 100 -t 10 -c 5 --pipeline=20 --hide-histogram --key-pattern=S:S -x 2  -s redis-12113.${cluster} -p 12113 -a jenkinstest'
            }
        }
        stage('Download Server Stats') {
            steps {
                script {
                    withCredentials(
			[usernamePassword(
				credentialsId: 'redis-cluster-creds',
				usernameVariable: 'REDIS_USERNAME',
			passwordVariable: 'REDIS_PASSWORD')]) {
                	sh '/mnt/host_machine/tools/dbstats-byname.py --redis-host ${params.cluster} --password $REDIS_PASSWORD --username $REDIS_USERNAME --db-name jenkins${BUILD_NUMBER}'
                     }
                }
            }
        }

    }

    post {

        always {
              script {
                  withCredentials(
                    [usernamePassword(
                    	credentialsId: 'redis-cluster-creds',
                    	usernameVariable: 'REDIS_USERNAME',
                    passwordVariable: 'REDIS_PASSWORD')]) {
              	sh '/mnt/host_machine/tools/rmdb-byname.py --redis-host ${params.cluster} --password $REDIS_PASSWORD --username $REDIS_USERNAME --db-name jenkins${BUILD_NUMBER} '
                   }
              }
          }
    }
}
