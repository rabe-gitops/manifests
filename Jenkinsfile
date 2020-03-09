pipeline {
  agent none

  /*** PIPELINE ENVIRONMENT ***/
  environment {
    BASE_PATH = 'base'
    OVERLAYS_PATH = 'overlays'
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
        label 'kubectl-slave' // image: boxboat/kubectl
      }

      steps {
        // select the 'kubectl' container
        container('kubectl') {
          sh """
            kubectl apply -k ${env.BASE_PATH} --dry-run -o yaml
            for d in \$(find "${env.OVERLAYS_PATH}" -mindepth 1 -maxdepth 1 -type d); do kubectl apply -k \${d} --dry-run -o yaml; done
          """
        }
      }
    }

    /** TAG SHIFT **/
    // stage('tag-shift') {

    //   when {
    //     // only for the master branch
    //     beforeAgent true
    //     branch 'master'
    //   }

    //   agent {
    //     // execute on the 'jenkins slave' pod
    //     label 'jenkins-slave' // image: jenkins/jnlp-slave:alpine
    //   }

    //   steps {
    //     // move latest release tag if not on HEAD (i.e. when manifests are updated)
    //     sh """
    //       read LATEST_TAG_COMMIT LATEST_TAG_NAME <<< \$(git ls-remote --tags --refs --sort='v:refname' origin | grep 'rel-*' | tail -n1 | sed -En 's|^([a-z0-9]+).*(rel-.*)\$|\\1 \\2|p')
    //       LATEST_COMMIT=\$(git rev-parse origin/master)
    //       if [ \${LATEST_TAG_COMMIT} != \${LATEST_COMMIT} ]; then \
    //         git push origin :refs/tags/\${LATEST_TAG_NAME} \
    //         git tag -f \${LATEST_TAG_NAME} \
    //         git push origin master --tags \
    //       fi
    //     """
    //   }
    // }
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
