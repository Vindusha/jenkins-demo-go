pipeline {
    agent any
    tools {
        go 'go1.14'
    }
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0 
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
        VERSION_NO         = '1.0'
        repo               = "vindu97/go-calc"
        registryCredential = 'dockerhub'
        dockerImg          = ''
    }
    stages {        
        stage('Pre Test') {
            steps {
                echo 'Installing dependencies'
                sh 'go version'
                sh 'go get -u golang.org/x/lint/golint'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Compiling and building'
                sh 'go build'
            }
        }

        stage('Test') {
            steps {
                withEnv(["PATH+GO=${GOPATH}/bin"]){
                    echo 'Running vetting'
                    sh 'go vet .'
                    echo 'Running linting'
                    sh 'golint .'
                    echo 'Running test'
                    sh 'cd test && go test -v'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying....'
                // Push to dockerhub image repository with tags per mergeid/featurebranch or etc.
                script{
                    dockerImg = docker.build repo+":$BUILD_NUMBER"
                    docker.withRegistry( '', registryCredential ) {
                        dockerImg.push()
                    }
                    sh 'docker rmi $repo:$BUILD_NUMBER'
                }
            }
        }
        
    }
    post {
        always {
            emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
                to: "${params.RECIPIENTS}",
                subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
            
        }
    }  
}