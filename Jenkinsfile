pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "sourav16031998/apachewebsite:latest"
        DOCKER_CREDENTIALS = "dockerhub-credentials"
    }

    stages {

        stage('Checkout Git') {
            steps {
                git url: 'https://github.com/Pvradmin-1234/apachewebsite.git', branch: 'master'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                ansiblePlaybook(
                    playbook: 'installapache.yml',
                    inventory: 'inventory.ini',
                    extras: '--private-key ssh14416681627219136652.key -u devops'
                )
            }
        }

        stage('Docker Build & Push') {
            steps {
                echo "Building Docker image..."
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        def app = docker.build(DOCKER_IMAGE, '-f Dockerfile .')
                        app.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f deployment.yml
                    kubectl apply -f service.yml
                '''
            }
        }

    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs!'
        }
    }
}
