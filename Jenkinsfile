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
                sh 'docker build -t anvbhaskar/docker_jenkins_pipeline:${BUILD_NUMBER} .'
            }
        }

       stage('Docker Login') {
    steps {
        script {
            def dockerLogin = sh(script: "docker login -u siva660 -p Red123@&&", returnStatus: true)
            if (dockerLogin != 0) {
                error "Failed to login to Docker Hub"
            }
        }
    }
}


        stage('Docker Push'){
            steps {
                sh 'docker push siva660/docker_jenkins_pipeline:${BUILD_NUMBER}'
            }
        }
        
        stage('Docker deploy'){
            steps {
                sh 'docker run -itd -p 8081:8080 siva660/springboot:0.0.3'
            }
        }

        stage('Archiving') { 
            steps {
                 archiveArtifacts '**/target/*.jar'
            }
        }
    }
}
