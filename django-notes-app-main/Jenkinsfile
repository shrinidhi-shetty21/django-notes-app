pipeline {
    agent any
    
    stages {
        stage ("Code") {
            steps {
                echo " Cloning the Code"
                git url:"https://github.com/shrinidhi-shetty21/django-notes-app" , branch: "main"
            }
        }
        stage ("Build"){
            steps {
                echo "Build the image"
                sh "docker build -t my-note-app ."
            }
        }
        stage ("Pushing the image to Dockerhub"){
            steps {
                echo "pushing image to docker hub"
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]) {
                sh "docker tag my-note-app ${env.dockerHubUser}/my-note-app:latest"
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "docker push ${env.dockerHubUser}/my-note-app:latest"
                }
            }
        }
        stage("Deploy"){
            steps {
                echo "Deploying the conatiner"
                sh "docker run -d -p 3001:3001 shrinidhi21/my-note-app:latest"
            }
        }
          stage('Deploy to Kubernets'){
             steps{
                 script{
                     dir('notesapp') {
                         withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubernetes', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                         sh 'kubectl delete --all pods'
                         sh 'kubectl apply -f deployment.yaml'
                         sh 'kubectl apply -f service.yaml'
                         }
                     }
                 }
             }
          }
    }
}