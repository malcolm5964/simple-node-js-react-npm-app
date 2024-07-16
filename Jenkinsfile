pipeline {
    agent any
    stages {
        stage('Build') { 
            steps {
                sh 'npm install' 
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('OWASP Dependency-Check Vulnerabilities') {
            steps {
                withCredentials([string(credentialsId: 'nvd-api-key', variable: 'NVD_API_KEY')]) {
                    dependencyCheck additionalArguments: ''' 
                                -o './'
                                -s './'
                                -f 'ALL' 
                                --prettyPrint
                                --nvdApiKey ${NVD_API_KEY}''', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'
                }
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
