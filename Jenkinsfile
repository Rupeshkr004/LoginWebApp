pipeline {
    agent any
    tools {
        maven "localMaven"
        jdk "java11"
    }

    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "34.121.188.227:8081" // Ensure to include protocol (http or https)
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "maven-releases"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "nexus"
        ARTIFACT_VERSION = "${BUILD_NUMBER}"
    }

    stages {
        stage("Check out") {
            steps {
                script {
                    git branch: 'master', url: 'https://github.com/Rupeshkr004/LoginWebApp.git';
                }
            }
        }

        stage("mvn build") {
            steps {
                script {
                    sh "mvn clean package"
                }
            }
        }

        stage("publish to nexus") {
            steps {
                script {
                    def pom = readMavenPom file: "pom.xml";
                    def filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    
                    if(filesByGlob) {
                        def artifactPath = filesByGlob[0].path;
                        echo "*** File: ${filesByGlob[0].name}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: ARTIFACT_VERSION,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
    }
}
