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
        stage('Approval') {
            steps {
                input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                sleep(time: 60, unit: 'SECONDS')
                sh './jenkins/scripts/kill.sh'
            }
        }
        stage('Send Build to EC2') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'EC2-Server',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'build/**',
                                    remoteDirectory: '/react-app',
                                    cleanRemote: true,
                                    execCommand: '''
                                        sudo cp -r /home/ubuntu/react-app/* /var/www/react-app/
                                    '''
                                )
                            ]
                        )
                    ]
                )
            }
        }
    }
}
