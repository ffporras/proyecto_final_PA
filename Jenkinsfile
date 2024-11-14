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
            choices: ['Jugar Trivia', 'Encargar Pedido', 'Traducir USQL'],
            description: 'Selecciona el módulo que deseas construir en esta ejecución del pipeline.'
        )
        choice(
            name: 'TEST_MODULE',
            choices: ['Entregable 1 - Trivia', 'Entregable 3 - USQL/SQL', 'Otro'],
            description: 'Selecciona el módulo para el cual deseas ejecutar pruebas unitarias en esta ejecución del pipeline. Elige "Handled Internally" si las pruebas ya se ejecutan en el módulo de concurrencia.'
        )
    }

    environment {
        DEPLOY_ENV = "${params.DEPLOY_ENVIRONMENT}"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                sh 'rm -rf entregable1final' //sh es como ejecutar un comando en la terminal
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

        stage('Build Selected Module') {
            when {
                expression { params.BUILD_MODULE == 'Jugar Trivia' }
            }
            steps {
                dir('entregable1final') { //dir es como cd en la terminal
                    sh "python3 src/trivia/main.py --jenkins"
                }
            }
        }

        stage('Build Concurrency Module') {
            steps {
                // Llama al job "Build Concurrency Module"
                build job: 'Build Concurrency Module'
            }
        }

        stage('Build USQL Module') {
            when {
                expression { params.BUILD_MODULE == 'Traducir USQL' }
            }
            steps {
                dir('entregable3_DSL') {
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

        stage('Unit Tests for Selected Module') {
            when {
                expression { params.TEST_MODULE != 'Otro' }
            }
            steps {
                script {
                    if (params.TEST_MODULE == 'Entregable 1 - Trivia') {
                        dir('entregable1final') {
                            sh 'python3 src/tests/testsdecorators.py'
                            sh 'python3 src/tests/testsmonads.py'
                            sh 'python3 src/tests/testsreader.py'
                        }
                    } else if (params.TEST_MODULE == 'Entregable 3 - USQL/SQL') {
                        dir('entregable3_DSL') {
                            sh 'python3 src/main/Test_traductorSQLaUSQL.py'
                            sh 'python3 src/main/Test_traductorUSQLaSQL.py'
                            sh 'python3 src/main/TestFluentAPI.py'
                        }
                    }
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