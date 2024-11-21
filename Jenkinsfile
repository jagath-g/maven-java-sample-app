pipeline {
    agent any
    
    tools {
        jdk 'JAVA'
    }


    stages {
        stage('git pull') {
            steps {
              git branch: 'master', url: 'https://github.com/MisbaHashimT/maven-java-sample-app.git'
            }
        }
        stage('Compile') {
            steps {
                bat "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                bat "mvn test"
            }
        }
        stage('Build') {
            steps {
               bat "mvn package"
            }
        }
        stage('push docker image') {
            steps {
               script {
                    // Docker image details
                    def dockerImage = 'jagathgiridharan/maven-java-sample-app'
                    def dockerTag = 'latest'

                    // Docker login
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    }

                    // Build the Docker image
                    bat "docker build -t ${dockerImage}:${dockerTag} ."

                    // Push the Docker image to Docker Hub
                    bat "docker push ${dockerImage}:${dockerTag}"
                } 
            }
        }
        stage('deploy application') {
            steps {
                script {
                    // Pull and run the Docker container
                    def dockerImage = 'jagathgiridharan/maven-java-sample-app'
                    def dockerTag = 'latest'
                    bat "docker pull ${dockerImage}:${dockerTag}"
                    bat "docker run -d -p 8001:8001 --name maven-app ${dockerImage}:${dockerTag}"
                }
            }
        }
        
    }
}
