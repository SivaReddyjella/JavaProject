pipeline {
    agent any 
    stages {
        stage('Compile and Clean') { 
            steps {
                sh "mvn clean compile"
            }
        }
        stage('Test') { 
            steps {
                sh "mvn test site"
            }
            
            post {
                always {
                    junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'   
                }
            }     
        }
        stage('deploy') { 
            steps {
                sh "mvn package"
            }
        }
        stage('Build Docker image'){
            
            steps {
                sh 'docker build -t siva660/docker_jenkins_pipeline:${BUILD_NUMBER} .'
            }
        }
      
    stage('Docker deploy') {
    steps {
        script {
            // Run the Docker container with the specified image and tag
            def dockerImageTag = "${BUILD_NUMBER}"
            sh "docker run -d -p 8082:8080 siva660/docker_jenkins_pipeline:${dockerImageTag}"
            sh "docker run -d -p 8082:8080 --name my_container siva660/docker_jenkins_pipeline:${dockerImageTag}"
        }
    }
}

        stage('Archving') { 
            steps {
                 archiveArtifacts '**/target/*.jar'
            }
        }
    }
}

