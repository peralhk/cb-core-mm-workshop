def label = "kaniko-${UUID.randomUUID().toString()}"

podTemplate(name: 'kaniko', label: label, yaml: """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: jenkins-docker-cfg
        mountPath: /kaniko/.docker
  volumes:
  - name: jenkins-docker-cfg
    projected:
      sources:
      - secret:
          name: abbvie-jfrog-regcred
          items:
            - key: .dockerconfigjson
              path: config.json
""") {
  node(label) {
        stage('Build with Kaniko') {

            git 'https://github.com/kypseli/cb-core-mm-workshop.git'
            container(name: 'kaniko', shell: '/busybox/sh') {
            withEnv(['PATH+EXTRA=/busybox']) {
            sh '''#!/busybox/sh
            /kaniko/executor --context `pwd` --destination abbvie-docker.jfrog.io/hello-kaniko:latest
            '''
           }
        }
      }
    }
  }