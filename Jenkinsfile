pipeline {
    agent any

    environment {
        APP_NAME = 'my-app'
        DOCKER_REPO = 'imanelo/my-app'
        VERSION_FILE = 'version.txt'
        DOCKERFILE_PATH = '/var/lib/jenkins/workspace/Jenkins-webhook-docker/Jenkins-WebhookGit-Docker/Dockerfile'
        GIT_CREDENTIALS_ID = 'Github-Jenkins'
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                script {
            // remove the Jenkins-WebhookGit-Docker subdirectory
            sh 'rm -rf Jenkins-WebhookGit-Docker'
        }
            }
        }

        stage('Checkout') {
            steps {
                script {
                    sh "git clone https://github.com/Imanelo/Jenkins-WebhookGit-Docker.git"
                    
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    
                    // Add debug logging
                    sh "ls -la Jenkins-WebhookGit-Docker" // List files in the current directory for debugging
                    dir('Jenkins-WebhookGit-Docker') {
                    // If only version.txt was modified, skip the pipeline
                    if (changes == VERSION_FILE) {
                    echo "Skipping pipeline, as only ${VERSION_FILE} was modified."
                    currentBuild.result = 'ABORTED'
                    return
                    }
                    // Read the current version from the file
                    def currentVersion = readFile(VERSION_FILE).trim()

                    // Increment the version
                    def newVersion = (currentVersion.toInteger() + 1).toString()

                    // Write the new version back to the file
                    writeFile file: VERSION_FILE, text: newVersion
                    // Add, commit, and push changes to the Git repository
                    // Commit and push the changes to the Git repository
                    withCredentials([usernamePassword(credentialsId: GIT_CREDENTIALS_ID, usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh 'git add version.txt'
                        sh "git commit -m 'Increment version in version.txt'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Imanelo/Jenkins-WebhookGit-Docker.git main"
                    }    
                        
                    // Build and tag the Docker image
                    sh "docker build -t ${DOCKER_REPO}:${newVersion} -f Dockerfile ."
                    sh "docker push ${DOCKER_REPO}:${newVersion}"
                    // Run the Docker image
                    sh "docker run -d -p 8081:80 --name ${APP_NAME} ${DOCKER_REPO}:${newVersion}"
                     }
                }
            }
        }
        

        // ... (rest of the stages remain unchanged)
    }

    post {
        success {
            // Cleanup actions on success (e.g., stop and remove the Docker container)
            script {
                sh "docker stop ${APP_NAME}"
                sh "docker rm ${APP_NAME}"
            }
        }
    }
}





