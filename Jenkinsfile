pipeline {
    agent any
    triggers {
        cron('* * * 1 *')
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
        stage('Copy ingress file fixes') {
            steps {
                script {
                    echo 'Copy secrets file ...'
                    sh 'cd ./ingress-fix/ && cp -rf * ../cluster-monitoring/manifests/ && cd .. && ls -l ./cluster-monitoring/manifests/ && cat ./cluster-monitoring/manifests/ingress-grafana.yaml'
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
