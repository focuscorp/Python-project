pipeline {
    agent none
    stages {
        stage('Build') {
          agent {
               docker {
                  image 'python:3-alpine'
               }
           }
           steps {
                sh 'python -m py_compile sources/*.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
           }
        }
        stage('Unit Test') {
          agent {
               docker {
                  image 'qnib/pytest:latest'
               }
          }
          steps {
               sh 'py.test --verbose --junit-xml test-reports/results.xml tests/*.py'
          }
          post {
               always {
                   junit 'test-reports/results.xml'
               }
          }
        }
        stage('Packaging') {
           agent any
               environment {
                   //VOLUME = '$(pwd)/sources:/src '
                   VOLUME = '$PWD/sources:/src'
                   IMAGE = 'cdrx/pyinstaller-linux:python3'
               }
               steps {
                   dir(path: env.BUILD_ID) {
                       unstash(name: 'compiled-results')

                       //sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"

                       //sh "docker run --rm -v ${VOLUME} ${IMAGE} 'python3 -m PyInstaller -F add2vals.py' "
                       sh "docker run --rm -v ${VOLUME} ${IMAGE} 'python3 -m PyInstaller -F add2vals.py'"
                   }
               }
               /*post {
                   success {
                        //This archiveArtifacts step archives the standalone executable file and exposes this file
                        //through the Jenkins interface.
                        archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                   }
               }*/
        }
    }
}
