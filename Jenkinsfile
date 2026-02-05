pipeline {
    agent any
    environment {
        HOME = "${WORKSPACE}"
        PIP_CACHE_DIR = "${WORKSPACE}/.pip-cache"
        PIP_DISABLE_PIP_VERSION_CHECK = "1"
    }
    stages {
        stage('SAST - Bandit') {
            agent { 
                docker { 
                    image 'python:3.12-slim' 
                } 
            }
            steps {
                sh '''
                    echo "Ejecutando análisis SAST con Bandit..."
                    python --version
                    python -m pip --version
                    python -m pip install --user --no-cache-dir bandit

                    set -eux
                    set +e
                    python -m bandit -r . --severity-level high --confidence-level high -f json -o bandit-report.json
                    BANDIT_EXIT_CODE=$?
                    set -e

                    if [ $BANDIT_EXIT_CODE -ne 0 ]; then
                        echo "Se encontraron hallazgos de severidad alta en Bandit."
                        exit 1
                    else
                        echo "No se encontraron hallazgos de severidad alta en Bandit."
                    fi
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
