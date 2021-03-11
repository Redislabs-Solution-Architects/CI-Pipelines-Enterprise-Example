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
                	sh '/mnt/host_machine/tools/mkdb-byname.py --redis-host mague.demo-azure.redislabs.com --password $REDIS_PASSWORD --username $REDIS_USERNAME --db-name jenkins${BUILD_NUMBER} --memory-size 107374182 --shard-count 2 --db-password jenkinstest --replication --db-port 12113'
                     }
                }
            }
        }
        stage('Wait for Redis') {
            steps {
		sh 'sleep 30'
            }
        }
        stage('Redis Info') {
            steps {
		sh 'redis-cli -h redis-12113.mague.demo-azure.redislabs.com -p 12113 -a jenkinstest INFO'
            }
        }
        stage('Memtier Benchmark') {
            steps {
		sh 'memtier_benchmark --ratio=1:4 --test-time=60 -d 100 -t 10 -c 5 --pipeline=50 --key-pattern=S:S -x 10  -s redis-12113.mague.demo-azure.redislabs.com -p 12113 -a jenkinstest'
            }
        }
        stage('Cleanup') {
              steps {
                  script {
                      withCredentials(
          		[usernamePassword(
          			credentialsId: 'redis-cluster-creds',
          			usernameVariable: 'REDIS_USERNAME',
          		passwordVariable: 'REDIS_PASSWORD')]) {
                  	sh '/mnt/host_machine/tools/rmdb-byname.py --redis-host mague.demo-azure.redislabs.com --password $REDIS_PASSWORD --username $REDIS_USERNAME --db-name jenkins${BUILD_NUMBER} '
                       }
                  }
              }
          }
    }
}
