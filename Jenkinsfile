pipeline {
    agent any
    stages {

        stage('SAST - Bandit') {
            steps {
                sh '''
                    echo "Ejecutando análisis SAST con Bandit..."
                    python --version
                    python -m pip --version
                    python -m pip install --user --no-cache-dir bandit

                    # Gate: solo severidad HIGH(y opcionalmente confianza HIGH)
                    python -m bandit -r . --severity-level high --confidence-level high -f json -o bandit-report.json
                '''
            }
            post {
                always {
                archiveArtifacts artifacts: 'bandit-report.json', fingerprint: true
                }
            }
        }

        stage('Build') {
            steps {
                sh 'echo "Building..."'
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Running tests..."'
            }
        }
    }
}
