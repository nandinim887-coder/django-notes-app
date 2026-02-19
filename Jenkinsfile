pipeline {
    agent any 
    
    environment {
        DOCKER_IMAGE = "my-note-app"
    }
    
    stages{
        
        stage("Clone Code"){
            steps {
                echo "Cloning the code"
                git branch: "main", url: "https://github.com/nandinim887-coder/django-notes-app.git"
            }
        }
        
        stage("Build Docker Image"){
            steps {
                echo "Building Docker image"
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }
        
        stage("Push to Docker Hub"){
            steps {
                echo "Pushing image to Docker Hub"
                
                withCredentials([usernamePassword(
                    credentialsId: "dockerHub",
                    usernameVariable: "DOCKER_USER",
                    passwordVariable: "DOCKER_PASS"
                )]) {
                    
                    sh "docker tag $DOCKER_IMAGE $DOCKER_USER/$DOCKER_IMAGE:latest"
                    
                    sh """
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_USER/$DOCKER_IMAGE:latest
                    """
                }
            }
        }
        
        stage("Deploy to Kubernetes"){
            steps {
                
                echo "Deploying to Kubernetes cluster"
                
                withKubeConfig(credentialsId: 'kubernetes') {
                    
                    sh "kubectl apply -f deployment.yaml"
                    sh "kubectl apply -f service.yaml"
                    
                }
            }
        }
    }
}
