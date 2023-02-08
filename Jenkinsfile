pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            args '-p 3000:3000' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                startStage()
                sh 'npm install' 
                endStage()
            }
        }        
        stage('Test') {
            steps {
                startStage()
                sh "chmod +x -R ${env.WORKSPACE}"
                sh './jenkins/scripts/test.sh'
                endStage()
            }
        }
        stage('Deliver') {
            steps {
                startStage()
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
                endStage()
            }
        }
        
    }
    
}

def void startStage() {
    // at the start of a stage all we need is to let our webapp know that the stage is in progress
    // no real data needed (probably)
    println("Beginning stage: ${STAGE_NAME} (sending webhook trigger...)")
    def response = httpRequest quiet:true, url: "http://httpbin.org/response-headers?stage=${STAGE_NAME}&status=Running"
    println('Status: ' + response.status)
    println('Response: ' + response.content)
}

def void endStage() {
    // at the end of a stage, we want to let our webapp know but also likely need to send some data
    // console logs, maybe artifacts, etc.
    println("Ending stage: ${STAGE_NAME} (sending webhook trigger...)") 
    def response = httpRequest quiet: true, url: "http://httpbin.org/response-headers?stage=${STAGE_NAME}&status=Complete"
    println('Status: ' + response.status)
    println('Response: ' + response.content)
}