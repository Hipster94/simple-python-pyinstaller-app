pipeline {
    agent {
        docker {
            image 'python:3-alpine'
            args '--entrypoint="" -w /workspace'
        }
    }
    environment {
        DEPLOY_SERVER = "localhost:4000"
        DEPLOY_PATH = "/opt/app"
    }
    stages {
        stage('Git Clone') { 
            steps {
                sh 'apk add --no-cache git'  // Ensure git is available
                git branch: 'master', url: 'https://github.com/jenkins-docs/simple-python-pyinstaller-app'
            }
        }
        stage('Build') {
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'python:3-alpine'
                    args '--entrypoint="" -w /workspace'
                }
            }
            steps {
                sh 'apk add --no-cache py3-pip'
                sh 'pip install pytest'
                sh 'pytest --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            agent {
                docker {
                    image 'python:3-alpine'
                    args '--entrypoint="" -w /workspace'
                }
            }
            steps {
                sh 'apk add --no-cache py3-pip'
                sh 'pip install pyinstaller'
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'python:3-alpine'
                    args '--entrypoint="" -w /workspace'
                }
            }
            steps {
                sh 'apk add --no-cache openssh-client'
                sh 'scp -o StrictHostKeyChecking=no dist/add2vals $DEPLOY_SERVER:$DEPLOY_PATH'
            }
        }
    }
}
