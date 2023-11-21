pipeline {
    agent any
    
    environment {
        JMETER_HOME = tool 'JMeter' // Debes configurar una herramienta de JMeter en la configuración de Jenkins
        JMX_FILE = 'Currencies.jmx'
        CSV_RESULT_FILE = 'resultado.csv'
        REPORT_OUTPUT_DIR = './ReporteCurrencies'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Run JMeter Test') {
            steps {
                script {
                    def jmeterCommand = "${JMETER_HOME}/bin/jmeter -n -t ${JMX_FILE} -l ${CSV_RESULT_FILE} -e -o ${REPORT_OUTPUT_DIR}"

                    sh jmeterCommand
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/*.csv'
            junit '**/*.xml'
        }
        success {
            echo 'La prueba de rendimiento se ejecutó exitosamente.'
        }
        failure {
            echo 'La prueba de rendimiento falló. Por favor, revisa los resultados y los informes generados.'
        }
    }
}