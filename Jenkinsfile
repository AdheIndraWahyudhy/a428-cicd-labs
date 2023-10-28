pipeline {
    agent {
        docker {
            image 'node:16-buster-slim'
            args '-p 3000:3000'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') { 
            steps {
                sh './jenkins/scripts/test.sh' 
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(message: 'Lanjutkan ke tahap deploy? (Klik "Proceed" untuk mengakhiri)', parameters: [booleanParam(defaultValue: true, description: 'Proceed to Deploy', name: 'Deploy')])
                    if (userInput == true) {
                        currentBuild.result = 'SUCCESS'
                    } else {
                        currentBuild.result = 'ABORTED'
                        error("Pipeline dihentikan oleh pengguna.")
                    }
                }
            }
        }
        stage('Deploy') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}