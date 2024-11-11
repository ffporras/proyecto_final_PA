pipeline {
    agent any

    environment {
        //Variables de entorno para credenciales de la nube, rutas de archivos, etc.
        DEPLOY_ENV = "production" 
    }

    stages {
        stage('Checkout') {
            steps {
                sh "git clone https://github.com/anaclaragelabert/entregable1final.git" //sh es para avisar que uso un comando bash
            }
        }

        stage('Build Game Module') {
            steps {
                dir('entregable1final') { //Es lo mismo que hacer cd
                    sh "python3 src/trivia/main.py"     
                }
            }
        }

         stage('Unit Tests') {
            steps {
                dir('entregable1final') {
                    sh 'pytest tests/'  
                }

            }
        }
    }
}