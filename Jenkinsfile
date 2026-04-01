pipeline {
    agent any

    stages {

        stage('Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t nodemain:v1.0 .'
                    } else {
                        sh 'docker build -t nodedev:v1.0 .'
                    }
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh '''
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image nodemain:v1.0
                        '''
                    } else {
                        sh '''
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image nodedev:v1.0
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh '''
                        docker stop nodemain || true
                        docker rm nodemain || true
                        docker run -d -p 3000:3000 nodemain:v1.0
                        '''
                    } else {
                        sh '''
                        docker stop nodedev || true
                        docker rm nodedev || true
                        docker run -d -p 3001:3000 nodedev:v1.0
                        '''
                    }
                }
            }
        }
    }
}
