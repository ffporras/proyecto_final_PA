pipeline {
    agent any

    parameters {
        choice(
            name: 'DEPLOY_ENVIRONMENT',
            choices: ['production', 'staging', 'development', 'testing'],
            description: 'Selecciona el entorno de despliegue. Esto determinará las variables y configuraciones aplicadas durante el proceso.'
        )
        choice(
            name: 'BUILD_MODULE',
            choices: ['Jugar al trivia', 'Encargar pedido', 'Traducir USQL'],
            description: 'Selecciona el módulo que deseas construir en esta ejecución del pipeline.'
        )
        choice(
            name: 'TEST_MODULE',
            choices: ['Entregable 1 - Trivia', 'Entregable 3 - USQL/SQL'],
            description: 'Selecciona el módulo para el cual deseas ejecutar pruebas unitarias en esta ejecución del pipeline.'
        )
    }

    environment {
        DEPLOY_ENV = "${params.DEPLOY_ENVIRONMENT}"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                // Elimina directorios locales de repositorios si existen
                sh 'rm -rf entregable1final'
                sh 'rm -rf Entregable2-Pedidos'
                sh 'rm -rf entregable3_DSL'
                cleanWs()
            }
        }

        stage('Check Python and Pip') {
            steps {
                sh 'which python3 || echo "Python not found"'
                sh 'python3 --version || echo "Python version check failed"'
                sh 'python3 -m pip --version || echo "Pip not found"'
            }
        }

        stage('Checkout') {
            steps {
                sh "git clone https://github.com/anaclaragelabert/entregable1final.git"
                sh "git clone https://github.com/ffporras/Entregable2-Pedidos.git"
                sh "git clone https://github.com/ffporras/entregable3_DSL.git"
            }
        }

        stage('Build Trivia Module') {
            when {
                expression { params.BUILD_MODULE == 'Jugar al trivia' }
            }
            steps {
                dir('entregable1final') {
                    echo "Ejecutando Trivia..."
                    sh "python3 src/trivia/main.py --jenkins"
                }
            }
        }

        stage('Build Concurrency Module') {
            when {
                expression { params.BUILD_MODULE == 'Encargar pedido' }
            }
            steps {
                echo "Ejecutando Pedidos..."
                build job: 'Build Concurrency Module'
            }
        }

        stage('Build USQL Module') {
            when {
                expression { params.BUILD_MODULE == 'Traducir USQL' }
            }
            steps {
                dir('entregable3_DSL') {
                    echo "Ejecutando Traducción USQL a SQL..."
                    sh "python3 src/main/main.py"
                }
            }
        }

        stage('Install dependencies for Entregable 3 - USQL/SQL') {
            when {
                expression { params.BUILD_MODULE == 'Traducir USQL' || params.TEST_MODULE == 'Entregable 3 - USQL/SQL' }
            }
            steps {
                sh 'python3 -m pip install ply'
                sh 'python3 -m pip install sqlparse'
                sh 'python3 -m pip install coverage'
            }
        }

        stage('Unit Tests for Trivia Module') {
            when {
                expression { params.TEST_MODULE == 'Entregable 1 - Trivia' }
            }
            steps {
                dir('entregable1final') {
                    echo "Ejecutando Pruebas de trivia..."
                    sh 'python3 src/tests/testsdecorators.py'
                    sh 'python3 src/tests/testsmonads.py'
                    sh 'python3 src/tests/testsreader.py'
                }
            }
        }

        stage('Unit Tests for USQL/SQL Module') {
            when {
                expression { params.TEST_MODULE == 'Entregable 3 - USQL/SQL' }
            }
            steps {
                dir('entregable3_DSL') {
                    echo "Ejecutando Pruebas de traducción..."
                    sh 'python3 src/main/Test_traductorSQLaUSQL.py'
                    sh 'python3 src/main/Test_traductorUSQLaSQL.py'
                    sh 'python3 src/main/TestFluentAPI.py'
                }
            }
        }
    }

    post {
        always {
            emailext (
                to: 'florenciaporras03@gmail.com',
                subject: "Pipeline Result: ${currentBuild.fullDisplayName}",
                body: """
                Resultado del pipeline: ${currentBuild.result}
                Detalles: ${env.BUILD_URL}
                """
            )
        }
        failure {
            emailext (
                to: 'florenciaporras03@gmail.com',
                subject: "Pipeline FAILED: ${currentBuild.fullDisplayName}",
                body: """
                Resultado del pipeline: ${currentBuild.result}
                Detalles: ${env.BUILD_URL}
                """
            )
        }
    }
}
