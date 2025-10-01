pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                git 'https://github.com/tu-usuario/mi-proyecto.git'
                bat  'echo "Construyendo..."'
            }
        }
        stage('Test') {
            steps {
                bat  'echo "Ejecutando pruebas..."'
            }
        }
        stage('Deploy') {
            steps {
                bat 'echo "Desplegando..."'
            }
        }
    }
}
