pipeline {
    agent any
    stages {
        stage('SonarQube analysis') {
            withSonarQubeEnv('My SonarQube Server') {
                  // requires SonarQube Scanner for Maven 3.2+
                sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.1492:sonar'
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    // Requires SonarQube Scanner for Jenkins 2.7+
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
    }
}