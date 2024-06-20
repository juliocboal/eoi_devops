pipeline {
    agent any
    environment {
        registry = "juliocboal/eoi-jenkins"
            registryCredentials = "dockerhub"
            project = "eoi-jenkins"
            projectVersion = "1.0"
            repository = "https://github.com/juliocboal/eoi_devops"
            repositoryCredentials = "github"
    }
    stages {
        stage('Limpiar Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout codigo') {
            steps {
                script {
                    git branch: 'main',
                    credentisId: repositoryCredentials,
                    url: repository
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Prueba') {
            steps {
                script {
                    try {
                        sh 'docker run --name $project $registry'
                    } finally {
                        sh 'docker rm $project'
                    }
                }
            }
        }
        stage('Despliegue') {
            steps {
                script {
                    docker.withRegistry('', registryCredentials) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Limpieza') {
            steps {
                script {
                    sh 'docker rmi $registry'
                }
            }
        }
    }
    post {
        failure {
            echo "El pipeline ha fallado"
        }
    }
}
