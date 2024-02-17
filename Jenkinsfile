pipeline {
    agent any
    tools {
        maven "maven"
    }
    environment {
        NEXUS_URL = 'http://3.121.185.194:8081'
        NEXUS_REPO = 'repository/today'
        GROUP_ID = 'new'
        ARTIFACT_ID = 'project'
        VERSION = '1.0'
        PACKAGING = 'war'
        CLASSIFIER = ''
        TOMCAT_URL = 'http://18.194.244.241:9090'
        TOMCAT_MANAGER_URL = "${TOMCAT_URL}/manager/text"
        TOMCAT_USERNAME = 'robot'
        TOMCAT_PASSWORD = 'rob@123'
        TOMCAT_CONTEXT_PATH = 'project'
    }
    stages {
        stage("checkout") {
            steps {
                git branch: 'main', credentialsId: 'bc333a0d-fdd6-4167-ba1c-b3770a9e0967', url: 'https://github.com/Kalal7553/jen.git'
            }
        }
        stage("build") {
            steps {
                sh "mvn install"
            }
        }
        stage("deploy to nexus") {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'project', classifier: '', file: 'target/project.war', type: 'war']], credentialsId: '3cc42c30-dc3e-4a58-ac46-a3be7acfed92', groupId: 'new', nexusUrl: '3.121.185.194:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'today', version: '1.0'
            }
        }
        stage('Deploy to Tomcat') {
            steps {
                script {
                    // Download the artifact from Nexus
                    def nexusArtifactUrl = "${NEXUS_URL}/${NEXUS_REPO}/${GROUP_ID}/${ARTIFACT_ID}/${VERSION}/${ARTIFACT_ID}-${VERSION}.${PACKAGING}"
                    sh "curl -O ${nexusArtifactUrl}"

                    // Deploy to Tomcat using manager-script
                    def deployCmd = "curl --upload-file ${ARTIFACT_ID}-${VERSION}.${PACKAGING} '${TOMCAT_MANAGER_URL}/deploy?path=/${TOMCAT_CONTEXT_PATH}&update=true' --user '${TOMCAT_USERNAME}:${TOMCAT_PASSWORD}'"
                    sh deployCmd

                    // Clean up downloaded artifact
                    sh "rm ${ARTIFACT_ID}-${VERSION}.${PACKAGING}"
                }
            }
        }
    }
}
