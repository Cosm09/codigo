pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t vulnerable-app .'
            }
        }

        stage('Run Container (Test)') {
            steps {
                sh 'docker run -d -p 5000:5000 --name app-test vulnerable-app'
            }
        }

        stage('Security Scan (ZAP Baseline)') {
            steps {
                sh '''
                docker run --rm -v $(pwd):/zap/wrk/ -t owasp/zap2docker-stable \
                zap-baseline.py -t http://localhost:5000 -r zap_report.html
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'zap_report.html', allowEmptyArchive: true
                }
            }
        }
    }
    
    post {
        always {
            echo "Pipeline completado."
        }
    }
}

