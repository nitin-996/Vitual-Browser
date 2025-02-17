pipeline {
    agent any
    
    environment {
        Scanner_Home = tool 'sonarScanner'  // Ensure 'sonarScanner' is correctly configured in Jenkins
    }

    stages {
        
        // SonarQube code quality analysis stage
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarServer') {  // Ensure 'sonarServer' is configured in Jenkins
                    sh '''
                        $Scanner_Home/bin/sonar-scanner \
                        -Dsonar.projectKey=VirtualBrowser \
                        -Dsonar.projectName=VirtualBrowser \
                        -Dsonar.sources=.
                    '''
                }
            }
        }

        // Docker build and push stage
        stage('Docker build') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker_user', toolName: 'docker') {  // No need for 'url' here for Docker Hub
                        dir('/var/lib/jenkins/workspace/virtual browser/.docker/firefox') {
                            
                            sh '''
                                docker build -t 007devopsimages/vb:latest . '''
                            // Removed duplicate push command here
                        }
                    }
                }
            }
        }

        // Trivy scan stage
        stage("Trivy scan") {
            steps {
                sh "trivy image 007devopsimages/vb:latest >> trivy.txt"
            }
        }

        // Docker push stage
        stage("Docker push") {
            steps {
                withDockerRegistry(credentialsId: 'docker_user' , url: 'https://hub.docker.com/') {  // No need for 'url' here for Docker Hub
                    sh "docker push 007devopsimages/vb:latest"
                }
            }
        }
    }
}
