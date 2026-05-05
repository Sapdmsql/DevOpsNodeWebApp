pipeline {
    agent any

    stages {
        stage('Docker Build & Login') {
            steps {
                script {
                    // Wir holen uns die Credentials für den Login
                    withCredentials([usernamePassword(credentialsId: 'DockerHub-sapdmsql', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''
                            export DOCKER_HOST=tcp://host.docker.internal:2375
                            
                            # 1. Image bauen (Das hat gefehlt!)
                            # Der Punkt am Ende steht für das aktuelle Verzeichnis (wo das Dockerfile liegt)
                            docker build -t sapdmsql/node-web-app:latest .
                            
                            # 2. Einloggen
                            echo $PASSWORD | docker login -u $USERNAME --password-stdin
                            
                            # 3. Jetzt existiert das Image lokal und kann gepusht werden
                            docker push sapdmsql/node-web-app:latest
                        '''
                    }
                }
            }
        }
        
        stage('Trigger Render Deployment') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'RenderDeployKey', variable: 'KEY')]) {
                        sh "curl https://api.render.com/deploy/$KEY"
                    }
                }
            }
        }        
    }
}