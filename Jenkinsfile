// Jenkinsfile (Declarative Pipeline)
/* Requires the Docker Pipeline plugin */
pipeline {
    agent any

    tools { go 'Go pipeline' }

    stages {

        stage('Install weaver') {
          steps {
            sh 'go get github.com/ServiceWeaver/weaver@latest'
            sh 'go install github.com/ServiceWeaver/weaver/cmd/weaver@latest'
            sh 'go install github.com/ServiceWeaver/weaver-kube/cmd/weaver-kube@latest'
          }
        }

        stage('Build Application') {
          steps {
            sh 'weaver generate .'
            sh 'go build .'
            sh 'weaver-kube deploy weaver.toml'
          }
        }


        }
    }
}
