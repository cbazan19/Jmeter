pipeline {
    agent any
    
    environment {
        JMETER_IMAGE = 'justb4/jmeter:latest'
        JMETER_HOME = '/apache-jmeter-5.6.2' 
        JMETER_TEST_FILE = 'Currencies.jmx'
        JMETER_RESULTS_FILE = 'report.jtl'
    }

    stages {
        stage('Preparar entorno') {
            steps {
                script {
                    // Clonar el repositorio Git (puedes ajustar la URL del repositorio)
                    checkout scm
                    
                    // Levantar el contenedor Docker de JMeter
                    bat "docker run -d --name jmeter-container -v ${workspace}:${JMETER_HOME} ${JMETER_IMAGE}"
                }
            }
        }
        
    stage('Configurar ambiente JMeter') {
        steps {
            script {
                // Copiar el archivo de prueba al contenedor Docker con los permisos necesarios
                bat "docker exec -u root jmeter-container cp ${workspace}/${JMETER_TEST_FILE} ${JMETER_HOME}/${JMETER_TEST_FILE}"
            }
        }
    }


        stage('Ejecutar pruebas JMeter') {
            steps {
                script {
                    // Ejecutar las pruebas JMeter dentro del contenedor Docker
                    bat "docker exec jmeter-container sh -c 'jmeter -jjmeter.save.saverservice.output_format=xml -n -t ${JMETER_HOME}/bin/${JMETER_TEST_FILE} -l ${JMETER_HOME}/bin/${JMETER_RESULTS_FILE}'"
                }
            }
        }

        stage('Guardar resultados') {
            steps {
                script {
                    // Copiar los resultados al directorio de trabajo de Jenkins
                    bat "docker cp jmeter-container:${JMETER_HOME}/bin/${JMETER_RESULTS_FILE} ${JMETER_RESULTS_FILE}"
                }
            }
        }
    }

    post {
        always {
            // Detener y eliminar el contenedor Docker después de ejecutar las pruebas
            script {
                bat "docker stop jmeter-container"
                bat "docker rm jmeter-container"
            }
        }
    }
}
