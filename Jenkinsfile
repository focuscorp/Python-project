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
                              stash(name: 'setUpPy', includes: 'setup.py*') 
               stash(name: 'pypirc', includes: '.pypirc') 
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
                   VOLUME = '$PWD/sources:/src' 
                   IMAGE = 'cdrx/pyinstaller-linux:python3' 
               } 
               steps { 
                   dir(path: env.BUILD_ID) { 
                       unstash(name: 'compiled-results') 
                       unstash(name: 'setUpPy') 
                       unstash(name: 'pypirc') 
                       //https://docs.python.org/3/distutils/builtdist.html 
                       sh 'cd sources'  
                        sh 'ls -l'  
                        sh 'python3 setup.py bdist_dumb --format=zip' 
                       sh 'python3 setup.py sdist bdist_wheel' 
                       sh 'python3 -m pip install --upgrade twine' 
                       sh 'python3 -m twine upload -r nexus-pypi dist/* --config-file .pypirc --verbose' 
                      
                       sh 'cd ..' 
                       sh 'whoami'
                       //sh 'sudo apt install wget'
                                             
                       sh 'wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | sudo apt-key add -' 
                       sh 'echo "deb https://packages.cloudfoundry.org/debian stable main" | sudo tee /etc/apt/sources.list.d/cloudfoundry-cli.list' 

                       sh 'sudo apt-get install cf8-cli'
                       //sh 'sudo apt-get install python3-django'
                       sh 'sudo apt-get install python3-pip'
                       sh 'pip3 install Django'
                       sh 'cf --help'
                       //sh 'cf login -a https://api.cf.us10.hana.ondemand.com/ -u mohtadi.nasri@focus-corporation.com -p 93407130Nm2021'
                       sh 'cf login -a https://api.cf.us10.hana.ondemand.com/ -u nadim.mabrouk@focus-corporation.com -p NADmab13446526='
                       //sh 'cf create-route cfapps.us10.hana.ondemand.com --hostname DjangoProject'
                       //sh 'cf map-route pythonproject cfapps.us10.hana.ondemand.com --hostname pythonproject'
                       sh 'cf push pythonproject  -b https://github.com/cloudfoundry/python-buildpack.git'
                    } 
               } 
               post { 
                   success { 
                        archiveArtifacts "${env.BUILD_ID}/dist/*" 
                   } 
               } 
        } 
    }
}

