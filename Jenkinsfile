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
        stage ('SCM update') {
            container('jnlp') {
                sh "docker version"
            }
        }
    }
}
