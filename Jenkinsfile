pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "98.88.253.148:8081"
        NEXUS_REPOSITORY = "devops"
        NEXUS_CREDENTIAL_ID = "nexus-creds"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/arjumand9794/arjmd_simplecutomerapp'
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
                    def packaging = pom.packaging
                    def version = pom.version

                    def files = findFiles(glob: "target/*.${packaging}")

                    if (files.length == 0) {
                        error "No ${packaging} file found in target folder"
                    }

                    def artifactPath = files[0].path

                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        groupId: pom.groupId,
                        version: version,
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        artifacts: [
                            [
                                artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: packaging
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
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
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
