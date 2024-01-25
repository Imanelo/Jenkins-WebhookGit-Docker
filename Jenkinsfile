pipeline{
    agent any 
    stages{
        stage("verifying tooling") {
            steps {
                sh '''
                docker version
                docker info
                docker compose version
                curl --version
                jq --version
                '''
            }
        }
        
        stage("Start Container") {
            steps {
                sh 'docker compose up -d --no-color --wait'
                sh 'docker compose ps'
            }
        }
        stage("Check Response") {
            steps {
                sh 'curl http://localhost'
            
            }
        }
    }
    post{
        always {
            sh 'docker compose down --remove-orphans -v'
            sh 'docker compose ps'
         }
    }
}




