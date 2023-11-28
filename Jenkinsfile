pipeline {
    agent any
    stages {
        stage('git scm update') {
            steps {
                git url: 'https://github.com/sheayun/pl-exp.git', branch: 'master'
            }
        }
        stage('docker build && push') {
            steps {
                sh '''
                docker build -t sheayun/simple-echo .
                docker push sheayun/simple-echo
                '''
            }
        }
        stage('deploy application on kubernetes cluster') {
            steps {
                sh '''
                kubectl create deployment dpy-simple-echo \
                    --image=sheayun/simple-echo
                kubectl expose deployment dpy-simple-echo \
                    --name=svc-simple-echo --type=LoadBalancer \
                    --port=30000 --target-port=80
                '''
            }
        }
    }
}
