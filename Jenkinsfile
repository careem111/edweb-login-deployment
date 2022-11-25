pipeline{
    agent {
     kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        metadata:
          name: mypod
        spec:
          containers:
          - name: build-agent
            image: careem785/jenkins-build-agent:2.0
            command: 
             - cat
            tty: true
            volumeMounts:
            - name: dockersock
              mountPath: /var/run/docker.sock
          volumes:
          - name: dockersock
            hostPath:
              path: /var/run/docker.sock  
      '''
        }
    }
    stages {
      stage('Checkout SCM') {
    	  steps{
	        container('build-agent'){
            sh 'mvn clean deploy -s settings.xml'
            }
	        }
        }      
      stage('Helm Chart'){
        steps{
          container('build-agent'){
              dir('charts') {
                withCredentials([usernamePassword(credentialsId: 'jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
                      sh '/usr/local/bin/helm repo add dptweb-helm-local  https://krmkube2.jfrog.io/artifactory/edweb-helm-local --username $username --password $password'
                      sh "/usr/local/bin/helm repo update"
                      sh "/usr/local/bin/helm install dptweb-qa --namespace qa -f values.yaml ."
                      sh "/usr/local/bin/helm list -a --namespace qa"
                  }
                }
             }
          }
      }
    }
}