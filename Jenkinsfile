pipeline {
    agent any
    stages {
        stage('Deploy to k3s') {
            when {
                expression {
                    env.BRANCH_NAME == 'master'
                }
            }
            agent {
                docker {
                    image 'registry-192.168.1.38.nip.io/homeassistant/helm-kubectl:latest'
                    args '-v /var/lib/jenkins/config:/var/lib/jenkins/config'
                }
            }
            steps {
                    echo 'Clone repo ...'
                    sh 'git clone https://github.com/carlosedp/cluster-monitoring.git'
                    echo 'Copy config file ...'
                    sh 'scp -F /var/lib/jenkins/.ssh/ pi@192.168.1.142:/media/mybook/cluster-monitoring-config/vars.jsonnet ./cluster-monitoring/vars.jsonnet'
                    sh 'execute make'
                    sh 'cd ./cluster-monitoring/ && ls -l && make vendor && make'
                    echo 'Deploying using helm...'
                    sh 'ls -l'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f manifests/setup/'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f manifests/'
                    sh 'sleep 5'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f manifests/'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
