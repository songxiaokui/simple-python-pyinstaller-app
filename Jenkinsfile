pipeline {
    agent none
    stages {
        stage("Build") {
            agent {
                docker {image 'python:2-alpine'}
            }
            steps {
                sh 'python --version'
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage("Test") {
            agent {
                docker {image 'qnib/pytest'}
            }
            steps {
                sh 'echo testing...'
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage("Ack") {
            steps {
                input "Does the staging environment look ok?"
            }
        }

        stage("Delivery") {
            agent {
                docker {image 'cdrx/pyinstaller-linux:python2'}
            }
            steps {
                sh 'echo start...'
                sh '/root/.pyenv/versions/2.7.17/bin/pyinstaller --onefile sources/add2vals.py'
            }
            post {
                always {
                    sh 'echo Done'
                }
                success {
                    archiveArtifacts 'dist/add2vals'
                }
                failure {
                    echo "deploy failed"
                }

            }
        }
    }
}