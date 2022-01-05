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
        eventTrigger jmespathQuery("repository.url=='https://github.com/dylanmeh/Lab3_1-4_Jenkinsfile'")
    }

    
    stages {
        stage ('Read api message for unitTestEnable = true string') {
            when { 
                allOf {
                    triggeredBy 'EventTriggerCause';
                    equals expected: 'unitTestEnabled = true', actual getTriggerCauseEvent()
                }
            }
            steps {
                echo 'kicking off unit tests'
            }
        }        
                
        stage ('Read api message for unitTestEnable = false string') {
            when {
                allOf {
                    triggeredBy 'EventTriggerCause';
                    equals expected: 'unitTestEnabled = false', actual: getTriggerCauseEvent()
                }
            }    
            steps {
                echo 'unit tests are disabled by user'
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
