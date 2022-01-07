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
        eventTrigger jmespathQuery("unitTestEnable=='true'")
    }
  
    stages {
        stage ('unit Tests enabled') {
            when { 
                allOf {
                triggeredBy 'EventTriggerCause';
                equals expected: 'true', actual: getTriggerCauseEvent() 
            }
            steps {
                echo 'Kicking off unit tests'
            }    
        }

        stage ('unit Tests disabled by user') {
            when { 
                allOf {
                triggeredBy 'EventTriggerCause';
                equals expected: 'false', actual: getTriggerCauseEvent()
            }    
            steps {
                echo 'Unit tests are disabled by user'
            }
        }
        
        stage ('buildStart Time Stage') {
            steps {
                buildStart ()
            }
        }
        stage ('build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage ('deploy') {
            steps {
                sh './scripts/deliver.sh'
                }
        }
        stage ('buildEnd Time Stage') {
            steps {
                buildEnd ()
            }
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