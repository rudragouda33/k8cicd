pipeline {
    agent any
    environment {
        registry = "rudragoudadockerhub/k8scicd"
        GOCACHE = "/tmp"
    }
    stages {
        stage('Build') {
            agent { 
                docker { 
                    image 'golang' 
                }
            }
            steps {
                // Create our project directory.
                sh 'cd ${GOPATH}/src'
                sh 'mkdir -p ${GOPATH}/src/hello-world'
                // Copy all files in our Jenkins workspace to our project directory.                
                sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
                // Build the app.
                sh 'go build'               
            }     
        }
        stage('Publish') {
            environment {
                registryCredential = 'dockerhub'
            }
            steps{
                script {
                    def appimage = docker.build registry + ":$BUILD_NUMBER"
                    docker.withRegistry( '', registryCredential ) {
                        appimage.push()
                        appimage.push('latest')
                    }
                }
            }
        }
        stage ('Deploy') {
            steps {
                script{
                    def image_id = registry + ":$BUILD_NUMBER"
                    sh "kubectl set image deployment/hello-deployment app=\"image_id=${image_id}\""
               }
            }
        }
    }
}
