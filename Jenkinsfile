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
                    // Obtener el SID del usuario usando PowerShell
                    def userSid = powershell(script: '([System.Security.Principal.WindowsIdentity]::GetCurrent()).User.Value', returnStdout: true).trim()
                    
                    // Ajustar los permisos del archivo usando el SID
                    bat "icacls \"${workspace}\\bin\\${JMETER_TEST_FILE}\" /grant ${userSid}:(R)"
                    
                    // Copiar el archivo de prueba al contenedor Docker
                    bat "docker cp ${workspace}/bin/${JMETER_TEST_FILE} jmeter-container:${JMETER_HOME}/bin/${JMETER_TEST_FILE}"
                }
            }
        }

        stage('Ejecutar pruebas JMeter') {
            steps {
                script {
                    // Ejecutar las pruebas JMeter dentro del contenedor Docker
                    bat "docker exec -u root jmeter-container sh -c 'cd ${JMETER_HOME}/bin && ./jmeter -jjmeter.save.saverservice.output_format=xml -n -t ${JMETER_TEST_FILE} -l ${JMETER_RESULTS_FILE}'"
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
