pipeline {
    agent any
    
     stages {
        stage('SCM') {
            steps {
                git url: 'https://github.com/blitznihar/simple-java-maven-app.git'
            }
        }
        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('AnsibleBuiltSonarqube') {
                    // Optionally use a Maven environment you've configured already
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar'
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
        stage ('Distribute binaries') { 
             steps {
                script {
                    def SERVER_ID = 'JFROG' 
                    def server = Artifactory.server SERVER_ID
                    def uploadSpec = 
                    """
                    {
                    "files": [
                        {
                            "pattern": "/target/(*).jar",
                            "target": "libs-snapshots-local/com/mycompany/app/{1}/"
                        },
                        {
                            "pattern": "/target/(*).war",
                            "target": "libs-snapshots-local/com/mycompany/app/{1}/"
                        }
                    ]
                    }
                    """
                    def buildInfo = Artifactory.newBuildInfo() 
                    buildInfo.env.capture = true 
                    buildInfo=server.upload(uploadSpec) 
                    server.publishBuildInfo(buildInfo) 
                }

             }
        }
    }
}