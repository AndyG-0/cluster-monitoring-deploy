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
                    sh 'cd ./ingress-fix/ && cp -rf * ../cluster-monitoring/manifests/ && cd ..'
                }
            }
        }
        stage('Copy vars file') {
            steps {
                script {
                    echo 'Copy vars file ...'
                    sh 'scp -F /var/lib/jenkins/.ssh/ pi@192.168.1.142:/srv/dev-disk-by-uuid-0A5DD4543FDF14C4/cluster-monitoring-config/vars.jsonnet ./cluster-monitoring/vars.jsonnet'
                }
            }
        }
        stage('Execute Make') {
            steps {
                script {
                    sh 'cd ./cluster-monitoring/ && ls -l && GO111MODULE=on | make vendor && make'
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
                    sh 'ls -l'
                    echo 'Apply PVs...'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f ./storage/'
                    echo 'Apply Setup...'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f cluster-monitoring/manifests/setup/'
                    echo 'Apply Manifests...'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && kubectl apply -f cluster-monitoring/manifests/'
                    sh 'sleep 5'
                    echo 'Apply Manifests...Again in case of a race condition'
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
