
pipeline {
  agent any

	tools {
		nodejs "NodeJS"
	}
	
	environment {
		PROJECT_ID = 'tech-rnd-project'
                CLUSTER_NAME = 'jenkins-jen-cluster'
                LOCATION = 'asia-south1-a'
                CREDENTIALS_ID = 'kubernetes'	
	}
	
    stages {
	    stage('Scm Checkout') {
		    steps {
			    	checkout scm
		    }
	    }
	    stage('Build') {
		    steps {
        	    		sh 'npm install'
		    }
	    }
			    
	    stage('Test') {
		    steps {
			   	echo "Testing..."
        			sh 'node test'
		    }
	    }
	    
	    stage('Build Docker Image') {
		    steps {
			    sh 'whoami'
			    script {
				    myimage = docker.build("fazilniveus/devops:${env.BUILD_ID}")
			    }
		    }
	    }
	    
	    stage("Push Docker Image") {
		    steps {
			    script {
				    echo "Push Docker Image"
				    withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
            				sh "docker login -u fazilniveus -p ${dockerhub}"
				    }
				        myimage.push("${env.BUILD_ID}")
				    
			    }
		    }
	    }
	    
	    stage('Deploy to K8s') {
		    steps{
			    echo "Deployment started ..."
			    sh 'ls -ltr'
			    sh 'pwd'
				sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
				echo "Start deployment of deployment.yaml"
				step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
			    	echo "Deployment Finished ..."
			    sh '''
			    '''
			    
		    }
	    }
    }
}
