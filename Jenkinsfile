stage('SSH Test') {
    steps {
        sshagent(['node-key']) {
            sh '''
            ssh -o StrictHostKeyChecking=no ubuntu@<3.109.60.232> "whoami && hostname"
            '''
        }
    }
}
