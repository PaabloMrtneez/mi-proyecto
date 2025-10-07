pipeline {
  agent any
  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Detect Python') {
      steps {
        script {
          // 1) try where python
          def out = bat(script: 'where python', returnStdout: true).trim()
          if (out) {
            env.PYTHON = out.split("\\r?\\n")[0].trim()
          } else {
            // 2) try py launcher (py -3)
            def pyOut = bat(script: 'py -3 -c "import sys;print(sys.executable)"', returnStdout: true).trim()
            if (pyOut) {
              env.PYTHON = pyOut.split("\\r?\\n")[0].trim()
            } else {
              // 3) check a couple of common locations (Program Files)
              def candidates = [
                'C:\\\\Program Files\\\\Python310\\\\python.exe',
                'C:\\\\Program Files (x86)\\\\Python310\\\\python.exe',
                'C:\\\\Program Files\\\\Python39\\\\python.exe'
              ]
              def found = ''
              for (c in candidates) {
                // evaluate existence
                def check = bat(script: "if exist \"${c}\" (echo FOUND) else (echo )", returnStdout: true).trim()
                if (check == 'FOUND') { found = c; break }
              }
              if (found) {
                env.PYTHON = found
              } else {
                error "Python no encontrado en el agente. Instala Python, añadelo al PATH del sistema, o configura Jenkins para usar un usuario con Python."
              }
            }
          }
          echo "Using python: ${env.PYTHON}"
        }
      }
    }

    stage('Setup venv and install deps') {
      steps {
        bat """
        \"${env.PYTHON}\" -m venv venv
        call venv\\Scripts\\activate
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        """
      }
    }

    stage('Run tests') {
      steps {
        bat """
        call venv\\Scripts\\activate
        if not exist reports mkdir reports
        python -m pytest --junitxml=reports\\results.xml
        """
      }
    }

    stage('Publish results') {
      steps { junit 'reports\\results.xml' }
    }
  }

  post {
    always { archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true }
  }
}
