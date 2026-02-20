pipeline {
    agent any

    environment {
        IMAGE_NAME = "isaurabhg/todo_app"
        K8S_DIR    = "k8s"
        NAMESPACE  = "default"
        SERVICE    = "django-todo"
        LOCAL_PORT = "8080"
        POD_PORT   = "80"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout https://github.com/isaurabhG/django-todo.git
            }
        }

        stage('Run Docker Container (Test)') {
            steps {
                sh '''
                  docker run -d --name myapp-test -p 8080:80 $IMAGE_NAME
                  sleep 5
                  docker rm -f myapp-test
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                  kubectl apply -f $K8S_DIR/ -n $NAMESPACE
                  kubectl rollout status deployment/myapp -n $NAMESPACE
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
