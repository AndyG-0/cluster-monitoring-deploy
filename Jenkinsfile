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
                    sh 'scp -F /var/lib/jenkins/.ssh/ pi@192.168.1.142:/media/mybook/cluster-monitoring-config/vars.jsonnet ./cluster-monitoring/vars.jsonnet'
                }
            }
        }
        stage('Make and Deploy to k3s') {
            when {
                expression {
                    env.BRANCH_NAME == 'master'
                }
            }
            steps {
                    echo 'Deploying using kubectl apply...'
                    sh 'ls -l'
                    sh 'export KUBECONFIG=/var/lib/jenkins/config && cd ./cluster-monitoring/ && make vendor && make && kubectl apply -f ./manifests/setup/ && kubectl apply -f ./manifests/ && sleep 5 && kubectl apply -f ./manifests/'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
