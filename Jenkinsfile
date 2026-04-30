pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    tools {
        maven '3.9.11'
    }

    environment {
        NEXUS_URL = "http://localhost:8081"
        NEXUS_REPO = "maven-releases"
        GROUP_ID = "com.example"
        ARTIFACT_ID = "gs-spring-boot"
        VERSION = "1.0.0"
    }

    stages {

        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/kevinli-webbertech/gs-spring-boot.git'
            }
        }

        stage('Test') {
            steps {
                sh 'git --version'
                sh 'mvn --version'
                sh 'mvn clean test'
            }
        }

        stage('Build and Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Upload to Nexus') {
            steps {
                script {
                    // Dynamically find the jar file
                    def jarFile = sh(
                        script: "ls target/*.jar | head -n 1",
                        returnStdout: true
                    ).trim()

                    echo "Uploading: ${jarFile}"

                    sh """
                    curl -v -u admin:e76beb8d-1123-496b-a89c-d0add3160d13 \
                    --upload-file ${jarFile} \
                    ${NEXUS_URL}/repository/${NEXUS_REPO}/com/example/gs-spring-boot/1.0.0/gs-spring-boot-1.0.0.jar
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build successful and artifact uploaded to Nexus!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
