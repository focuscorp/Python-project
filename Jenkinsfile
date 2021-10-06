@Library('piper-lib-os') _

node() {
   
    stage('Init') {
       cleanWs()
       checkout scm
       setupCommonPipelineEnvironment script:this
       
    }
    stage('Build Stage') {
       mavenExecute(
         script: this,
         goals: ['install']
      )
    }
}
