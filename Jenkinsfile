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
        stage('Docker Login'){  
           steps{
            withCredentials([string(credentialsId: 'DockerId', variable: 'Dockerpwd')]) {
             sh "docker login -u siva660 -p ${Dockerpwd}"
            }
           }
       }      
  stage('Docker deploy') {
    steps {
        script {
            def dockerImageTag = "${BUILD_NUMBER}"
            def containerId = sh(script: "docker run -d -p 8082:8080 siva660/docker_jenkins_pipeline:${dockerImageTag}", returnStdout: true).trim()

            if (containerId) {
                echo "Docker container started successfully. Container ID: ${containerId}"
            } else {
                error "Failed to start Docker container."
            }
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

