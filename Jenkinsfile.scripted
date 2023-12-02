POD_TEMPLATE_YAML = '''
apiVersion: v1
Kind: Pod
spec:
  containers:
  - name: jnlp
    image: sheayun/jnlp-agent-sample
    command:
    - /entrypoint.sh
    env:
    - name: DOCKER_HOST
      value: "tcp://localhost:2375"
  - name: dind
    image: docker:latest
    command:
    - /usr/local/bin/dockerd-entrypoint.sh
    env:
    - name: DOCKER_TLS_CERTDIR
      value: ""
    securityContext:
      privileged: true
'''

podTemplate(label: 'jenkins-agent-sample', yaml: POD_TEMPLATE_YAML) {
    node('jenkins-agent-sample') {
        stage('SCM update') {
            checkout scm
        }
        stage('Docker build & push') {
            dockerImage = docker.build "sheayun/simple-echo"
            docker.withRegistry('https://registry.hub.docker.com',
                'dockerhub-credentials') {
                dockerImage.push("latest")
            }
        }
        stage('Deploy on k8s cluster') {
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
