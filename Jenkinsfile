pipeline {
    agent any

    stages {
        stage('Clone the project') {
            steps {
                sh '''
                    cd /projects
                    if [ -d /projects/bgapp-docker ]; then
                        cd /projects/bgapp-docker
                        git pull https://github.com/enchoenchev/do-m4-bgapp.git
                    else
                        git clone https://github.com/enchoenchev/do-m4-bgapp.git bgapp-docker
                    fi
                    '''
            }
        }
        stage('Build the image') {
            steps {
                sh 'cd /projects/bgapp-docker && docker image build -t bgapp-db -f Dockerfile.db .'
                sh 'cd /projects/bgapp-docker && docker image build -t bgapp-web -f Dockerfile.web .'
            }   
        }
        stage('Create the network') {
            steps {
                sh '''
                    cd /projects/bgapp-docker
                    docker network ls | grep app-network || docker network create app-network
                    '''
            }
        }
        stage('Run the application') {
            steps {
                sh '''
                    cd /projects/bgapp-docker
                    docker container ls | grep bgapp-db && docker container rm -f bgapp-db
                    docker container ls | grep bgapp-web && docker container rm -f bgapp-web
                    docker container run -d --name bgapp-db --net app-network -e MYSQL_ROOT_PASSWORD=12345 bgapp-db
                    docker container run -d --name bgapp-web -p 80:80 --net app-network -v /projects/bgapp-docker/web:/var/www/html:ro bgapp-web
                    '''
            }
        }
    }
}