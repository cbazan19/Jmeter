pipeline {
    agent any

    environment {
        JMETER_IMAGE = 'justb4/jmeter:5.5'
        JMETER_SCRIPT = 'Currencies.jmx'
        JMETER_REPORT = './bin/Run1.jtl'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Clonar el repositorio
                    checkout scm
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Copiar el archivo JMX al directorio bin de la imagen de Docker
                    bat "docker run --rm -v ${WORKSPACE}:${WORKSPACE} -w ${WORKSPACE} ${JMETER_IMAGE} cp ${JMETER_SCRIPT} bin/"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Ejecutar el contenedor de Docker con JMeter
                    bat "docker run --rm -v ${WORKSPACE}:/jmeter -w /jmeter ${JMETER_IMAGE} -n -t bin/${JMETER_SCRIPT} -l ${JMETER_REPORT}"
                }
            }
        }
    }

    /*post {
        always {
            // Puedes realizar tareas adicionales después de la ejecución
        }
    }*/
}
