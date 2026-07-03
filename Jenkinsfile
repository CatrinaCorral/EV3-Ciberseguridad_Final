pipeline {
  agent any

  environment {
    APP_URL       = 'http://ev3-app:5000'
    REPORT_DIR    = 'zap-reports'
  }

  stages {

    stage('Build') {
      steps {
        sh '''
          docker network create devsecops-network || true
          docker build -t ev3-ciberseguridad_final:${BUILD_NUMBER} .
        '''
      }
    }

    stage('Test - Unit') {
      steps {
        sh '''
          python3 -m venv venv || true
          . venv/bin/activate || true

          pip install --upgrade pip || true
          pip install -r requirements.txt || true
          pip install pytest flask || true

          pytest --junitxml=results.xml || true
        '''
        junit allowEmptyResults: true, testResults: 'results.xml'
      }
    }

    stage('Deploy - Staging') {
      steps {
        sh '''
          docker rm -f ev3-app || true

          docker run -d \
            --name ev3-app \
            --network devsecops-network \
            -p 5000:5000 \
            ev3-ciberseguridad_final:${BUILD_NUMBER}

          sleep 5

          docker run --rm \
            --network devsecops-network \
            curlimages/curl:latest \
            -sf http://ev3-app:5000 || echo "App no responde"
        '''
      }
    }

    stage('Security Test - SCA Dependencies') {
      steps {
        sh """
          docker volume rm dc-report-vol 2>/dev/null || true
          docker volume create dc-report-vol
          docker volume create dc-nvd-data || true

          docker run --rm \
            --network devsecops-network \
            -v \${WORKSPACE}:/src:ro \
            -v dc-report-vol:/report \
            -v dc-nvd-data:/usr/share/dependency-check/data \
            owasp/dependency-check:latest \
              --scan /src \
              --format HTML \
              --format XML \
              --out /report \
              --project EV3-Ciberseguridad \
              --noupdate || true

          docker run --rm \
            -v dc-report-vol:/report \
            alpine ls -la /report/ || true

          docker run --rm \
            -v dc-report-vol:/report \
            alpine cat /report/dependency-check-report.xml || true

          mkdir -p \${WORKSPACE}/dc-report
        """

        sh '''
          docker run --rm \
            -v dc-report-vol:/report \
            -v ${WORKSPACE}/dc-report:/dest \
            alpine sh -c 'cp /report/*.html /dest/ 2>/dev/null || true; cp /report/*.xml /dest/ 2>/dev/null || true; chmod 644 /dest/* 2>/dev/null || true'

          echo "=== Archivos copiados al workspace ==="
          ls -la ${WORKSPACE}/dc-report/ || true
        '''

        publishHTML(target: [
          allowMissing         : true,
          alwaysLinkToLastBuild: true,
          keepAll              : true,
          reportDir            : "${WORKSPACE}/dc-report",
          reportFiles          : 'dependency-check-report.html',
          reportName           : 'Dependency-Check Report'
        ])
      }
    }

    stage('Security Test - DAST ZAP') {
      steps {
        sh """
          docker volume rm zap-report-vol 2>/dev/null || true
          docker volume create zap-report-vol

          docker run --rm \
            -v zap-report-vol:/zap/wrk \
            alpine sh -c 'chmod 777 /zap/wrk'

          echo "=== Probando acceso a la aplicacion ==="
          docker run --rm \
            --network devsecops-network \
            curlimages/curl:latest \
            -sf http://ev3-app:5000 || echo "App no responde"

          echo "=== Ejecutando OWASP ZAP Baseline Scan ==="
          docker run --rm \
            -u root \
            --network devsecops-network \
            -v zap-report-vol:/zap/wrk/:rw \
            ghcr.io/zaproxy/zaproxy:stable \
              zap-baseline.py \
                -t http://ev3-app:5000 \
                -r zap_report.html \
                -J zap_report.json \
                --auto || true

          echo "=== Contenido volumen ZAP ==="
          docker run --rm \
            -v zap-report-vol:/zap/wrk \
            alpine ls -la /zap/wrk || true

          rm -rf \${WORKSPACE}/zap-reports
          mkdir -p \${WORKSPACE}/zap-reports

          docker rm -f zap-copy 2>/dev/null || true

          docker create --name zap-copy \
            -v zap-report-vol:/report \
            alpine

          docker cp zap-copy:/report/. \${WORKSPACE}/zap-reports/ || true
          docker rm zap-copy || true

          chmod 644 \${WORKSPACE}/zap-reports/* 2>/dev/null || true

          echo "=== Contenido zap-reports en workspace ==="
          ls -la \${WORKSPACE}/zap-reports/ || true
        """

        publishHTML(target: [
          allowMissing         : true,
          alwaysLinkToLastBuild: true,
          keepAll              : true,
          reportDir            : "${WORKSPACE}/zap-reports",
          reportFiles          : 'zap_report.html',
          reportName           : 'OWASP ZAP Report'
        ])

        sh 'find ${WORKSPACE} -name "*.html" -o -name "*.xml" -o -name "*.json" 2>/dev/null | head -30'
      }
    }

    stage('Generate Documentation') {
      steps {
        sh '''
          mkdir -p ${WORKSPACE}/docs-report

          echo "==============================================" >  ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "# Trazabilidad de Seguridad - Build #${BUILD_NUMBER}" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "Fecha: $(date)" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "Commit: ${GIT_COMMIT}" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "## Etapas ejecutadas en este build" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "- Build: imagen ev3-ciberseguridad_final:${BUILD_NUMBER} construida" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "- Test - Unit: resultados en results.xml" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "- Deploy - Staging: contenedor desplegado en devsecops-network" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "- Security Test - SCA Dependencies: ver dependency-check-report.html" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "- Security Test - DAST ZAP: ver zap_report.html" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "## Documentacion asociada del repositorio" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "- DEPENDENCY_MANAGEMENT.md" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "- SDLC_SECURITY_DOCUMENTATION.md" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
          echo "==============================================" >> ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md

          cat ${WORKSPACE}/docs-report/trazabilidad_build_${BUILD_NUMBER}.md
        '''
      }
    }

    stage('Version Control - Docs') {
      steps {
        sh '''
          echo "=== Versionando documentacion generada para build #${BUILD_NUMBER} ==="
          ls -la ${WORKSPACE}/docs-report/
          echo "El archivo de trazabilidad queda archivado como artefacto versionado por numero de build."
        '''
        archiveArtifacts(
          artifacts        : 'docs-report/*.md',
          allowEmptyArchive: true
        )
      }
    }
  }

  post {
    always {
      sh """
        cp \${WORKSPACE}/dc-report/*.html \${WORKSPACE}/ 2>/dev/null || true
        cp \${WORKSPACE}/dc-report/*.xml \${WORKSPACE}/ 2>/dev/null || true
        cp \${WORKSPACE}/zap-reports/*.html \${WORKSPACE}/ 2>/dev/null || true
        cp \${WORKSPACE}/zap-reports/*.json \${WORKSPACE}/ 2>/dev/null || true
      """

      archiveArtifacts(
        artifacts         : 'dependency-check-report.html,dependency-check-report.xml,zap_report.html,zap_report.json,results.xml',
        allowEmptyArchive : true
      )

      sh 'docker rm -f ev3-app || true'
    }
  }
}
