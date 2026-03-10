pipeline {
    agent any

    environment {
        DHURL = "http://172.17.0.2/"
        DHUSER = "admin"
        DHPASS = "admin"
        IMAGE_TAG = "${BUILD_NUMBER}"
        BLDDATE = sh(script: "date", returnStdout: true).trim()
        SHORT_SHA = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
        PATH = "$HOME/.local/bin:$PATH"
    }

    stages {
        stage('Build Image') {
            steps {
                sh 'docker build -t python-web:${BUILD_NUMBER} -f ./Docker/Dockerfile .'
            }
        }

        stage('Generate SBOM') {
            steps {
                sh '''
                syft python-web:${BUILD_NUMBER} -o cyclonedx-json > cyclonedx.json
                cat cyclonedx.json
                '''
            }
        }

        stage('Publish to Ortelius') {
            steps {
                sh '''
                dh updatecomp --dhurl $DHURL \
                  --dhuser $DHUSER \
                  --dhpass $DHPASS \
                  --rsp component.toml \
                  --deppkg "cyclonedx@cyclonedx.json"
                '''
            }
        }
    }
}
