pipeline {
    agent none
    stages {
        stage('install python'){
            steps{
                //sh 'apt-get -y install python3-pip'
                //sh 'pip install -r requirements.txt'
                echo 'Installation is Already Done by Docker Agent_'
            }
        }
        stage('Build') {
          agent {
               docker {
                  //This image parameter downloads the qnib:pytest Docker image and runs this image as a
                  //separate container. The pytest container becomes the agent that Jenkins uses to run the Test
                  //stage of your Pipeline project.
                  image 'python:3-alpine'
               }
           }
           steps {
                //This sh step runs the Python command to compile your application and
                //its calc library into byte code files, which are placed into the sources workspace directory
                sh 'python -m py_compile sources/*.py'
                //This stash step saves the Python source code and compiled byte code files from the sources
                //workspace directory for use in later stages.
                stash(name: 'compiled-results', includes: 'sources/*.py*')
                preserveStashes()
           }
        }

        stage('Unit Test') {
          agent {
               docker {
                  //This image parameter downloads the qnib:pytest Docker image and runs this image as a
                  //separate container. The pytest container becomes the agent that Jenkins uses to run the Test
                  //stage of your Pipeline project.
                  image 'qnib/pytest:latest'
               }
          }
          steps {
               //sh 'python3 --version'
               sh 'py.test --verbose --junit-xml test-reports/results.xml tests/*.py'
          }
          post {
               always {
                   //This junit stepd archives the JUnit XML report (generated by the py.test command above) and
                   //exposes the results through the Jenkins interface.
                   //The post section’s always condition that contains this junit step ensures that the step is
                   //always executed at the completion of the Test stage, regardless of the stage’s outcome.
                   junit 'test-reports/results.xml'
               }
          }
        }


        //Deliver
        stage('Packaging') {
           agent any
               //This environment block defines two variables which will be used later in the 'Deliver' stage
               environment {
                   //VOLUME = '$(pwd)/sources:/src '
                   VOLUME = '$PWD/sources/:/src/'
                   IMAGE = 'cdrx/pyinstaller-linux:python3'
               }
               steps {
                   //This dir step creates a new subdirectory named by the build number.
                   //The final program will be created in that directory by pyinstaller.
                   //BUILD_ID is one of the pre-defined Jenkins environment variacbles.
                   //This unstash step restores the Python source code and compiled byte
                   //code files (with .pyc extension) from the previously saved stash. image]
                   //and runs this image as a separate container.
                   dir(path: env.BUILD_ID) {
                       unstash(name: 'compiled-results')

                       //This sh step executes the pyinstaller  command (in the PyInstaller container)
                       //on your simple Python application.
                       //This bundles your add2vals.py  Python application into a single standalone executable file
                       //and outputs this file to the dist workspace directonry (within the Jenkins home directory).
                       //sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"

                       //sh 'cat sources'
                       //used PyInstaller as Module
                       //sh "pwd"


                       //sh "cd /src"
                       //sh "ls -l"

                        // docker run <arguments> <image> <command>
                        //sh "docker run --rm -v ${VOLUME} ${IMAGE} 'python3 -m PyInstaller -F add2vals.py' "
                        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'python3 -m PyInstaller -F --onefile add2vals.py'"


                   }
               }
               /*post {
                   success {
                        //This archiveArtifacts step archives the standalone executable file and exposes this file
                        //through the Jenkins interface.
                        archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
                        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
                   }
               } */
        }


    }
}
