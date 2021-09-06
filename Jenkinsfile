pipeline {
    agent none
    parameters {
            string(name: 'SRC', defaultValue: 'sources', description: 'Resources Directory ')
            string(name: 'TESTS', defaultValue: 'tests', description: 'Test Directory ')
    }
    stages {
        stage('install python'){
            steps{
                //sh 'apt-get -y install python3-pip'
                //sh 'pip install -r requirements.txt'
                echo 'Installation is Already Done by Docker Agent'
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
                 echo '${parameters.SRC}'
                //This sh step runs the Python command to compile your application and
                //its calc library into byte code files, which are placed into the sources workspace directory
                sh 'python -m py_compile ${parameters.SRC}/*.py'
                //This stash step saves the Python source code and compiled byte code files from the sources
                //workspace directory for use in later stages.
                stash(name: 'compiled-results', includes: '${parameters.SRC}/*.py*')

                echo 'helloworld'
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
                sh 'py.test --verbose --junit-xml test-reports/results.xml ${params.TESTS}/*.py'
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
    }
}
