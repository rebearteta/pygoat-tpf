pipeline {
    agent any
    environment {
        HOME = "${WORKSPACE}"
        PIP_CACHE_DIR = "${WORKSPACE}/.pip-cache"
        PIP_DISABLE_PIP_VERSION_CHECK = "1"
        PROJECT_NAME = 'pygoat-tf'
        PROJECT_VER = "build-${BUILD_NUMBER}"
        SBOM_FILE = 'bom.xml'
    }
    stages {
        stage('SCA - Dependency-Track') {
            agent { 
                docker { 
                    image 'python:3.12-slim' 
                } 
            }
            steps {
                sh '''
                    echo "Generando SBOM con CycloneDX..."
                    set -eux
                    python3 -m venv .venv
                    . .venv/bin/activate
            
                    python -m pip install -U pip cyclonedx-bom

                    echo "Detectando dependencias y generando SBOM..."
                    if [ -f requirements.txt ]; then
                        cyclonedx-py requirements requirements.txt --of XML --sv 1.6 -o "${SBOM_FILE}"
                    else
                        cyclonedx-py environment --of XML --sv 1.6 -o "${SBOM_FILE}"
                    fi
                    
                    echo "SBOM generado"
                    echo "Subiendo SBOM a Dependency-Track..."
                '''
                // Llamada a step de Jenkins para subir SBOM
                dependencyTrackPublisher(
                    projectName: "${env.PROJECT_NAME}",
                    projectVersion: "${env.PROJECT_VER}",
                    artifact: "${env.SBOM_FILE}",
                    synchronous: true
                )
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
