pipeline {
    agent any
    stages {
        stage('Build') {
            // Build the code using Maven
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Unit and Integration Tests') {
            // Run unit tests using JUnit and integration tests using Selenium
            steps {
                sh 'mvn test'
            }
        }
        stage('Code Analysis') {
            // Analyze the code using SonarQube
            steps {
                sh 'mvn sonar:sonar'
            }
        }
        stage('Security Scan') {
            // Scan the code for vulnerabilities using OWASP ZAP
            steps {
                sh 'zap-daemon -port 8080 -config api.disablekey=true'
                sh 'zap-cli -p 8080 quick-scan http://localhost:8080/myapp'
                sh 'zap-cli -p 8080 report -o zap-report.html -f html'
                sh 'zap-cli -p 8080 shutdown'
            }
        }
        stage('Deploy to Staging') {
            // Deploy the application to a staging server using AWS CLI
            steps {
                sh 'aws s3 cp target/myapp.war s3://myapp-bucket'
                sh 'aws deploy create-deployment --application-name myapp --deployment-group-name staging --s3-location bucket=myapp-bucket,key=myapp.war,bundleType=zip'
            }
        }
        stage('Integration Tests on Staging') {
            // Run integration tests on the staging environment using Selenium
            steps {
                sh 'mvn test -P staging'
            }
        }
        stage('Deploy to Production') {
            // Deploy the application to a production server using AWS CLI
            steps {
                sh 'aws s3 cp target/myapp.war s3://myapp-bucket'
                sh 'aws deploy create-deployment --application-name myapp --deployment-group-name production --s3-location bucket=myapp-bucket,key=myapp.war,bundleType=zip'
            }
        }
    }
    post {
        success {
            // Send a success email with logs as attachment
            emailext subject: 'Pipeline Success', body: 'The pipeline completed successfully.', to: 'schewang2001@email.com', attachLog: true
        }
        failure {
            // Send a failure email with logs as attachment
            emailext subject: 'Pipeline Failure', body: 'The pipeline failed.', to: 'schewang2001@email.com', attachLog: true
        }
    }
}
