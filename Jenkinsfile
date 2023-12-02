pipeline {
    agent {
        kubernetes {
            yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: jnlp
                image: sheayun/jnlp-agent-sample
                env:
                - name: DOCKER_HOST
                  value: "tcp://localhost:2375"
              - name: dind
                image: docker:latest
                command:
                - /usr/local/bin/dockerd-entrypoint.sh
                securityContext:
                  privileged: true
                env:
                - name: DOCKER_TLS_CERTDIR
                  value: ""
            '''
        }
    }
    stages {
        stage('git scm update') {
            steps {
                git url: 'https://github.com/sheayun/pl-exp.git',
                    branch: 'master'
            }
        }
        stage('docker build && push') {
            steps {
                script {
                    dockerImage = docker.build "sheayun/simple-echo"
                    docker.withRegistry('https://registry.hub.docker.com',
                        'dockerhub-credentials') {
                        dockerImage.push("latest")
                    }
                }
            }
        }
        stage('deploy application on kubernetes cluster') {
            steps {
                withKubeConfig([credentialsId: 'KUBECONFIG',
                    serverUrl: 'https://kubernetes.default',
                    namespace: 'default']) {
                    sh '''
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    '''
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
