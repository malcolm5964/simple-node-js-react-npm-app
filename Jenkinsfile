pipeline {
    agent any
    environment {
        NVD_API_KEY = credentials('nvd-api-key') // Ensure the correct credential ID
    }
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
                script {
                    def additionalArgs = """
                        -o './'
                        -s './'
                        -f 'ALL'
                        --prettyPrint
                        --nvdApiKey ${env.NVD_API_KEY}
                    """
                    dependencyCheck additionalArguments: additionalArgs, odcInstallation: 'OWASP Dependency-Check Vulnerabilities'
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
