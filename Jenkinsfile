pipeline {
    agent any
 
    
    stages {
	stage('Pre Test') {
    echo 'Pulling Dependencies'

    sh 'go version'
    sh 'go get -u github.com/golang/dep/cmd/dep'
    sh 'go get -u github.com/golang/lint/golint'
    sh 'go get github.com/tebeka/go2xunit'
    sh 'cd $GOPATH/src/cmd/project && dep ensure'
}

stage('Build'){
    echo 'Building Executable'

    //Produced binary is $GOPATH/src/cmd/project/project
    sh """cd $GOPATH/src/cmd/project/ && go build -ldflags '-s'"""
}

stage('BitBucket Publish'){

    //Find out commit hash
    sh 'git rev-parse HEAD > commit'
    def commit = readFile('commit').trim()

    //Find out current branch
    sh 'git name-rev --name-only HEAD > GIT_BRANCH'
    def branch = readFile('GIT_BRANCH').trim()

    //strip off repo-name/origin/ (optional)
    branch = branch.substring(branch.lastIndexOf('/') + 1)

    def archive = "${GOPATH}/project-${branch}-${commit}.tar.gz"

    echo "Building Archive ${archive}"

    sh """tar -cvzf ${archive} $GOPATH/src/cmd/project/project"""

    echo "Uploading ${archive} to BitBucket Downloads"
    withCredentials([string(credentialsId: 'bb-upload-key', variable: 'KEY')]) { 
        sh """curl -s -u 'user:${KEY}' -X POST 'Downloads Page URL' --form files=@'${archive}' --fail"""
    }
}


    }
}
