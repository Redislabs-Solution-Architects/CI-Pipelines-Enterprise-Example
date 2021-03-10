/* Note you will have to go to 
*  https://JENKINSSERVER/scriptApproval/ and approve the necessary perms
*  eg=> new java.io.File java.lang.String
* staticMethod org.codehaus.groovy.runtime.DefaultGroovyMethods getText java.io.File
*/

pipeline {
  
  stages {
    stage('Prepare') {
      steps {
        deleteDir()
        checkout scm
        echo 'beginnning workflow...'
      }
    }

    stage('Back-end') {
        agent {
            docker { image 'redislabs/redis:latest' }
        }
    }

    stage('Set terraform version') {
      steps {
        sh '''#!/bin/bash
        id
        '''
      }
    }

  }

}
