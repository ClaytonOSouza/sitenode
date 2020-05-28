podTemplate(
    label: 'worker', 
    containers: [
        containerTemplate(args: 'cat', name: 'docker', command: '/bin/sh -c', image: 'docker', ttyEnabled: true)
    ],
    volumes: [
      hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
    ]
){
    node('worker') {
        echo 'Iniciando Pipeline'
        stage('Clone') {
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'scm-global', url: 'https://github.com/ClaytonOSouza/sitenode.git']]])
            sh 'ls -ltra'
        }
        stage('test') {
            container('docker') {
                sh 'docker ps'
            }
        }
         stage('Building our image') {
	    steps{
	        script {
		    dockerImage = docker.build registry + ":$BUILD_NUMBER"
	       }
	  }	 
    }
	stage('Deploy our image') {
	    steps{
	        script {
	            docker.withRegistry( '', registryCredential ) {
			dockerImage.push()
	              }	
	        }	 	
	  }
    }	
	 stage('Cleaning up') {
	     steps{
	         sh "docker rmi $registry:$BUILD_NUMBER"
		}
         }
   }
}
