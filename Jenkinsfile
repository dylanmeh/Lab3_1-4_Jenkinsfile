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
        stage ('Read event data') {
            steps {
                script {
                    def buildCauseInfo = currentBuild.getBuildCauses("com.cloudbees.jenkins.plugin.pipeline.events.EventTriggerCause")
                    if (buildCauseInfo && buildCauseInfo[0]) {
                        String artifactId = buildCauseInfo[0].event.environment
                        return artifactId
                    }
                    return "N/A"
                }
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