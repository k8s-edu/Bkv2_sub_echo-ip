pipeline {
  agent any
  stages {
    stage('git scm update') {
      steps {
        git url: 'https://github.com/k8s-edu/Bkv2_sub_echo-ip.git', branch: 'main'
      }
    }   
    stage('docker build and push') {
      steps {
        script {
          def image = docker.build("library/echo-ip")
          docker.withRegistry("https://192.168.1.10:8443", "harbor-credential"){
              image.push("latest")
          }
        }
      }
    }
    stage('deploy kubernetes') {
      steps {
        sh '''
        kubectl create deployment pl-echo-ip --image=192.168.1.10:8443/library/echo-ip \
                                               -n default
        kubectl expose deployment pl-echo-ip --type=LoadBalancer --port=80 \
                                               --target-port=80 --name=pl-echo-ip-svc \
                                               -n default
        '''
      }
    }
  }
}
