pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "98.88.253.148:8081"
        NEXUS_REPOSITORY = "devops"          // storing artifacts in devops repo
        NEXUS_CREDENTIAL_ID = "nexus-creds"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/arjumand9794/arjmd_simplecutomerapp.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Publish to Nexus') {
            steps {
                script {

                    def pom = readMavenPom file: 'pom.xml'
                    def artifactPath = "target/${pom.artifactId}-${pom.version}.jar"

                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        groupId: pom.groupId,
                        version: pom.version,
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        artifacts: [
                            [
                                artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: 'jar'
                            ],
                            [
                                artifactId: pom.artifactId,
                                classifier: '',
                                file: 'pom.xml',
                                type: 'pom'
                            ]
                        ]
                    )
                }
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build Successful'
        }
        failure {
            echo 'Build Failed'
        }
    }
}
