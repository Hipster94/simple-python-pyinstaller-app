pipeline {
    agent {
        docker {
            image 'python:3-alpine'
            args '--entrypoint=""'
        }
    }
    stages {
        stage('Git Clone') { 
            steps {
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
                    args '--entrypoint=""'
                }
            }
            steps {
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
                    args '--entrypoint=""'
                }
            }
            steps {
                sh 'pip install pyinstaller'
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
