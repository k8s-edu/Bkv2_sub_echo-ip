pipeline {
  agent any
  stages {
    stage('git scm update') {
      steps {
        git url: 'https://github.com/IaC-Sources/echo-ip.git', branch: 'main'
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
        kubectl create deployment pl-bulk-prod --image=192.168.1.10:8443/library/echo-ip
        kubectl expose deployment pl-bulk-prod --type=LoadBalancer --port=8080 \
                                               --target-port=80 --name=pl-bulk-prod-svc
        '''
      }
    }
  }
}
