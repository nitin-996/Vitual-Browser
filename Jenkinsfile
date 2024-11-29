pipeline {
    agent any
    
    environment {
        Scanner_Home = tool 'sonarScanner'  // Ensure 'sonarScanner' is correctly configured in Jenkins
    }

    stages {
        
        // stage("Git Checkout") {  // Corrected stage name
        //     steps {  // Missing 'steps' block
        //         git branch: 'main', credentialsId: 'githubToken', url: 'https://github.com/nitin-996/Virtual-Browser.git'
        //     }
        // }

        // Vulnerability check stage using Dependency Check plugin
        // stage('Vulnerability Check') {
        //     steps {
        //         dependencyCheck additionalArguments: '--scan ./ --disableNodeAudit', 
        //                         odcInstallation: 'DP'
        //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }

        // SonarQube code quality analysis stage
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarServer') {  // Ensure 'sonarServer' is configured in Jenkins
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
                    withDockerRegistry(credentialsId: 'docker_user', toolName: 'Docker') {
                        dir('/var/lib/jenkins/workspace/virtual browser/.docker/firefox') {
                            sh 'docker build -t 007devopsimages/VB:latest .'
                            sh 'docker push 007devopsimages/VB:latest'  // Corrected image name for push
                        }
                    }
                }
            }
        }

        stage("trivy scan"){
            steps{
                sh "trivy image 007devopsimages/VB:latest >> trivy.txt"
            }
        }

        stage("dicker push"){
            steps{
                withDockerRegistry(credentialsId: 'docker_user', toolName: 'Docker') {
                    sh "docker push 007devopsimages/VB:latest"
               }
            }

        }
    }
}
