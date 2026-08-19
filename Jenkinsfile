pipeline {
    agent any
    
    environment {
        // Define the app image and Docker credentials
        appimage = "intdoc89/bankapp:latest"
    }
    
   stages {
        stage('Build and Analyze') {
            steps {
                // Build the project and run SonarQube analysis
               withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                sh """
                /opt/maven/bin/mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                -Dsonar.projectKey=Ateef-Ahmed_Multi-Tier-With-Database \
                -Dsonar.organization=ateef-ahmed \
                -Dsonar.host.url=https://sonarcloud.io \
                -Dsonar.token=$SONAR_TOKEN
                """
            }
        }
    } 

       stage('Test') {
            steps {
                // Run Maven package to ensure tests are executed
                sh "/opt/maven/bin/mvn package"
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                sh "docker build -t ${appimage} ."
            }
        }
   
        stage('Docker Login and Push') {
            steps {
                // Log in to DockerHub and push the image
                withCredentials([usernamePassword(
                    credentialsId: 'dockerpwd',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                    )]) {
                sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                sh "docker push ${appimage}"
            }
            }
        

        stage('Deploy to Kubernetes') {
            steps {
                // Deploy the app using Ansible playbook
               // sh "ansible-playbook localhost ansible.yaml"
                  sh "kubectl apply -f ds.yml"
            }
        }
    }
}
}
