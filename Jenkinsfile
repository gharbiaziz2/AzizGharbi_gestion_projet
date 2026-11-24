pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        registry = "azizghrab/gestion-projet"
        registryCredential = 'dockerHub'
    }

    stages {

        stage('GIT') {
            steps {
                echo 'Récupération du code...'
                git branch: 'main', url: 'https://github.com/gharbiaziz2/AzizGharbi_gestion_projet.git'
            }
        }

        stage('MVN CLEAN') {
            steps {
                echo 'Cleaning Maven project...'
                dir('backend') {
                    sh 'mvn clean'
                }
            }
        }

        stage('ARTIFACT CONSTRUCTION') {
            steps {
                echo 'Building Spring Boot application...'
                dir('backend') {
                    sh 'mvn package -DskipTests'
                }
            }
        }

        stage('UNIT TESTS') {
            steps {
                echo 'Running unit tests...'
                dir('backend') {
                    sh 'mvn test'
                }
            }
        }

        stage('DOCKER BUILD') {
            steps {
                echo 'Building Docker image...'
                dir('backend') {
                    sh "docker build -t ${registry}:${BUILD_NUMBER} -t ${registry}:latest ."
                }
            }
        }

        stage('DOCKER PUSH') {
            steps {
                echo 'Pushing image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: registryCredential,
                                 usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${registry}:${BUILD_NUMBER}"
                    sh "docker push ${registry}:latest"
                }
            }
        }
    }
}
