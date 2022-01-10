@Library("lab3") _
pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: build
    image: 'maven:3.8.3-jdk-11'
    command:
    - cat
    tty: true
    '''
        defaultContainer 'build'
        }
    }
    
    triggers {
        eventTrigger jmespathQuery("environment=='prod'")
    }
  
    stages {
        stage ('Enable unit testing when event is prod') {
            if (getTriggerCauseEvent.getTriggerCauseEvent() == 'prod')
                echo 'enabling unit testing'
            }
            return "N/A"    
        }
        stage ('Disable unit testing when event is dev') {
            if (getTriggerCauseEvent.getTriggerCauseEvent() == 'dev')
                echo 'user disabled unit testing'
            }
            return "N/A"
        }        
        stage ('buildStart Time Stage') {
            buildStart ()
        }
        stage ('build') {
            sh 'mvn -B -DskipTests clean package'
        }
        stage('Test') {
            sh 'mvn test'
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage ('deploy') {
            sh './scripts/deliver.sh'
        }
        stage ('buildEnd Time Stage') {
                buildEnd ()
        }
    }
        post {
            success {
                buildResultsEmail("Successful")                
            }
            
            failure {
                buildResultsEmail("Failure")
            }       
            
    }
}