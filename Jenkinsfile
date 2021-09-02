pipeline {
    agent any
    stages {

        stage('install python'){
            steps{
                sh 'apt-get -y install python3-pip'
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Build') {
            steps {
                  //This sh step runs the Python command to compile your application and
                //its calc library into byte code files, which are placed into the sources workspace directory
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                //This stash step saves the Python source code and compiled byte code files from the sources
                //workspace directory for use in later stages.
                //stash(name: 'compiled-results', includes: 'sources/*.py*')

                echo 'helloworld'
                }
        }

        stage('Unit Test') {
            steps {
            sh 'python3 --version'
        }
        }
    }
}
