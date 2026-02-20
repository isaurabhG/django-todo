pipeline {
    agent any

    environment {
        IMAGE_NAME = "isaurabhg/todo_app:latest"
        K8S_DIR    = "k8s"
        NAMESPACE  = "default"
        SERVICE    = "django-todo"
        LOCAL_PORT = "8081"
        POD_PORT   = "80"
        DEPLOYMENT = "django-todo-deployment"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/isaurabhG/django-todo.git', branch: 'develop'
            }
        }

        stage('Run Docker Container (Test)') {
            steps {
                sh '''
                  docker rm -f myapp-test || true
                  docker pull $IMAGE_NAME
                  docker run -d --name myapp-test -p 8081:8000 $IMAGE_NAME
                  sleep 5
                  docker rm -f myapp-test
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                  kubectl apply -f $K8S_DIR/ -n $NAMESPACE
                  kubectl rollout status deployment/$DEPLOYMENT -n $NAMESPACE
                '''
            }
        }

        stage('Temporary Port Forward (Debug)') {
            steps {
                sh '''
                  timeout 15 kubectl port-forward svc/$SERVICE $LOCAL_PORT:$POD_PORT -n $NAMESPACE || true
                '''
            }
        }
    }
}
