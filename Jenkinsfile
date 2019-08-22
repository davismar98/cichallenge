pipeline {
	environment {
		CHALLENGE_GIT="https://github.com/davismar98/cichallenge.git"
		FOLDER_NAME="challenge"
		DOCKER_USER="davismar98"
		DOCKER_IMAGE_NAME="nodeapp"
	}
	agent any         
		stages {                 
			stage('Prepare') {                         
				steps {                                 
					echo 'Preparing..'
					sh "rm -rf $FOLDER_NAME"
					sh "mkdir -p $WORKSPACE/challenge;\
						git config --global user.email 'david.martinez@endava.com';\
						git config --global user.name 'David Martínez';\
						git config --global push.default simple;\
						git clone $CHALLENGE_GIT $FOLDER_NAME"
					sh "chmod -R +x $WORKSPACE/$FOLDER_NAME"
				}                 
			}                 
			stage('Build') {                         
				steps {                                 
					echo 'Building..'
					sh 'cd $WORKSPACE/$FOLDER_NAME'
					sh 'docker build -t $DOCKER_USER/$DOCKER_IMAGE_NAME $WORKSPACE/$FOLDER_NAME'                         
				}                 
			}                 
			stage('Test') {
				agent {
					docker { image '$DOCKER_USER/$DOCKER_IMAGE_NAME:latest' }
				}                         
				steps {                                 
					echo 'Testing...' 
					sh 'cd /usr/src/app && npm test'
				}                 
			}
			stage('push') {
				steps {
					echo 'pushing'
					withDockerRegistry([ credentialsId: "docker-hub", url:""]) {
                      // following commands will be executed within logged docker registry
                      sh 'docker push $DOCKER_USER/$DOCKER_IMAGE_NAME:latest'
                   }
				}
			}                 
			stage('Deploy') {                         
				steps {                                 
					echo 'Deploying....'
					sh 'docker run -d -p 6060:8000 --name $DOCKER_IMAGE_NAME $DOCKER_USER/$DOCKER_IMAGE_NAME'
				    //sh 'curl -i 0.0.0.0:6060'
				    input 'Do you want to delete the app?'
				    sh 'docker stop $DOCKER_IMAGE_NAME && docker rm $DOCKER_IMAGE_NAME'
				}                 
			}         
		} 
} 
