pipeline {
    agent any
    
    environment {
        // Define the app image and Docker credentials
        appimage = "intdoc89/bankapp:latest"
        dockerhubpwd = credentials('dockerpwd') // Replace 'DOCKERHUB_PASSWORD' with the appropriate credentials ID
    }
    

        
        stage('Build and Analyze') {
            steps {
                // Build the project and run SonarQube analysis
                sh """
                /usr/share/maven/bin/mvn verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                -Dsonar.projectKey=zafar90_Multi-Tier-With-Database \
                -Dsonar.organization=ateef-ahmed \
                -Dsonar.host.url=https://sonarcloud.io \
                -Dsonar.login=df3b7313f7c62476b0fb7cddb7655c46a96e1b41
                """
            }
        }

        stage('Test') {
            steps {
                // Run Maven package to ensure tests are executed
                sh "/usr/share/maven/bin/mvn package"
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
                sh "docker login -u intdoc89 -p ${dockerhubpwd}"
                sh "docker push ${appimage}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Deploy the app using Ansible playbook
                //sh "ansible-playbook -i inventory.ini ds.yml"
                sh "kubectl apply -f ds.yml"
            }
        }
    }
}
