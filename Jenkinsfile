pipeline {
  agent none

  /*** PIPELINE ENVIRONMENT ***/
  environment {
    BASE_PATH = 'base'
    OVERLAYS_PATH = 'overlays'
    SLAVES_TEMPLATES_PATH = 'slaves'
  }

  /*** STAGES ***/
  stages {

    /** VERIFY **/
    stage('verify') {

      when {
        // only for the master branch
        beforeAgent true
        branch 'master'
      }

      agent {
        // execute on the 'kubectl slave' pod
        kubernetes {
          defaultContainer 'kubectl'
          yamlFile "${SLAVES_TEMPLATES_PATH}/kubectl-slave.yaml"
        }
      }

      steps {
        sh """
          kubectl apply -k ${env.BASE_PATH} --dry-run -o yaml
          for d in \$(find "${env.OVERLAYS_PATH}" -mindepth 1 -maxdepth 1 -type d); do kubectl apply -k \${d} --dry-run -o yaml; done
        """
      }
    }
  }

  // /*** POST-EXECUTION ***/
  // post {
  //   success {
  //     node('ci-jenkins-slave') {
  //       echo 'SUCCESS'
  //     }
  //   }
  //   failure {
  //     node('ci-jenkins-slave') {
  //       echo 'FAILURE'
  //     }
  //   }
  //   always {
  //     node('ci-jenkins-slave') {
  //       echo 'ENDED'
  //     }
  //   }
  // }
}
