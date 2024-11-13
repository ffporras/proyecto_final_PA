pipeline {
    agent any

    environment {
        //Variables de entorno para credenciales de la nube, rutas de archivos, etc.
        DEPLOY_ENV = "production" 
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
                sh "git clone https://github.com/anaclaragelabert/entregable1final.git" //sh es para avisar que uso un comando bash
                //sh "git clone https://github.com/ffporras/Entregable2-Pedidos.git"
                sh "git clone https://github.com/ffporras/entregable3_DSL.git"
            }
        }

        stage('Build Game Module') {
            steps {
                dir('entregable1final') { //Es lo mismo que hacer cd
                    sh "python3 src/trivia/main.py --jenkins"     
                }
            }
        }

        stage('Unit Tests for Entregable 1 - Trivia') {
            steps {
                dir('entregable1final') {
                    sh 'python3 src/tests/testsdecorators.py'  
                    sh 'python3 src/tests/testsmonads.py'
                    sh 'python3 src/tests/testsreader.py'
                }

            }
        }
        //stage('Build Concurrency Module') {
        //    steps {
        //        dir('Entregable2-Pedidos') {
        //            // Ejecuta Maven para compilar el proyecto
        //            //sh 'mvn clean install'
        //        }
        //    }
        //}

        //stage('Unit Tests for Entregable 2 - Concurrency') {
        //    steps {
        //        dir('Entregable2-Pedidos') {
                // Ejecuta las pruebas unitarias con Maven
                    //sh 'mvn test'
        //        }
        //    }
        //}

        stage('Install dependencies for Entregable 3 - USQL/SQL') {
            steps {
                sh 'python3 -m pip install ply'
            }
        }

        stage('USQL Module') {
            steps {
                dir('entregable3_DSL') { //Es lo mismo que hacer cd
                    sh "python3 src/main/main.py"     
                }
            }
        }

        stage('Unit Tests for Entregable 3 - USQL/SQL') {
            steps {
                dir('entregable3_DSL') {
                    sh 'python3 src/main/Test_traductorSQLaUSQL.py'  
                    sh 'python3 src/main/Test_traductorUSQLaSQL.py'
                    sh 'python3 src/main/TestFluentAPI.py'
                }

            }
        }
    }
}