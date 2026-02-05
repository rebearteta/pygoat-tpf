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
            steps {
                sh '''
                    echo "Generando SBOM con CycloneDX..."
                    python --version
                    python -m pip --version
                    python -m pip install --user --no-cache-dir cyclonedx-bom
                    python -m cyclonedx-py --of XML --sv 1.6 -o  ${SBOM_FILE}

                    echo "Subiendo SBOM a Dependency-Track..."
                    dependencyTrackPublisher(
                        projectName: "${env.PROJECT_NAME}",
                        projectVersion: "${env.PROJECT_VER}",
                        artifact: "${env.SBOM_FILE}",
                        synchronous: true
                    )
                '''
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
