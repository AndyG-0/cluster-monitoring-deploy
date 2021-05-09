pipeline {
    agent any
    triggers {
        cron('H * * * 1 *')
    }
    stages {
        stage('Clone repo') {
            steps {
                script {
                    echo 'Clone repo ...'
                    sh 'git clone https://github.com/carlosedp/cluster-monitoring.git'
                }
            }
        }
        stage('Copy vars file') {
            steps {
                script {
                    echo 'Copy secrets file ...'
                    sh 'scp -F /var/lib/jenkins/.ssh/ pi@192.168.1.142:/media/mybook/cluster-monitoring-config/vars.jsonnet ./cluster-monitoring/vars.jsonnet'
                }
            }
        }
        stage('Execute Make') {
            steps {
                script {
                    sh 'cd ./cluster-monitoring/ && ls -l && make vendor && make'
                }
            }
        }
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
                    echo 'Deploying using kubectl apply...'
                    sh 'ls -l'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f cluster-monitoring/manifests/setup/'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f cluster-monitoring/manifests/'
                    sh 'sleep 5'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f cluster-monitoring/manifests/'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
