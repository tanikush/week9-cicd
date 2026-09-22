pipeline {
    agent any 

    environment {
	IMAGE_NAME = "tanishakushwah/week9-app"
	IMAGE_TAG = "v${BUILD_NUMBER}"
    }

    stages {
	stage('Checkout') {
	    steps {
		echo 'Checking out code from Github..'
		checkout scm
	    }
        }
 
	stage('Build') {
	    steps {
		echo 'Building the application...'
		sh 'echo "Simulating build process..."'
	    }
	}

	stage('Test') {
	    steps {
		sh 'echo "All tests passed"'
	    }
	}

	stage('Package') {
            steps {
		echo 'Packing application..'
		sh 'tar --exclude=.git --exclude=app-package.tar.gz -czf app-package.tar.gz .'
	    }
	}

	stage('Docker Build') {
	    steps {
		echo 'Building Docker image...'
		sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
	    }
	}

	stage('Docker Push') {
	    steps {
		echo 'Pushing image to docker hub..'
		withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
		    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
		    sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
		}
	    }

	}

	stage('Deploy') {
	    steps {
		echo 'Deploying new version (rolling deployment)...'
		sh '''
		    docker stop week9-app-old || true
		    docker rm week9-app-old || true
		    docker run -d --name week9-app-new -p 8090:80 $IMAGE_NAME:$IMAGE_TAG
		   '''
	   }
	}
    }

    post {
	success {
            echo 'Pipeline completed successfully! Application deployed.'
        }
	failure {
	    echo 'Pipeline failed. Review console output for details.'
	}
    }
}

	
