pipeline {
    agent any
    
    environment {
        JMETER_HOME = tool 'JMeter' // Configura una herramienta de JMeter en la configuración de Jenkins
        JMX_FILE = 'Currencies.jmx'
        JTL_RESULT_FILE = 'resultado.jtl'
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
                    def jmeterCommand = "${JMETER_HOME}/bin/jmeter -n -t ${JMX_FILE} -l ${JTL_RESULT_FILE} -e -o ${REPORT_OUTPUT_DIR}"

                    sh jmeterCommand
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/*.jtl'
            junit '**/*.xml'
        }
        success {
            echo 'La prueba de rendimiento se ejecutó exitosamente.'
            step([$class: 'ArtifactArchiver', artifacts: 'ReporteCurrencies/**/*', allowEmptyArchive: true])
            step([$class: 'PublishPerformanceReport', testResults: '**/target/*.jtl', relativeFailedThresholdNegative: 1.0, relativeFailedThresholdPositive: 1.0, relativeUnstableThresholdNegative: 1.0, relativeUnstableThresholdPositive: 1.0])
        }
        failure {
            echo 'La prueba de rendimiento falló. Por favor, revisa los resultados y los informes generados.'
        }
    }
}
