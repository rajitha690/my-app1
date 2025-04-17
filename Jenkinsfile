pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER = 'sonar' // Your SonarQube installation name in Jenkins
    }

    stages {

        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    withSonarQubeEnv("${env.SONARQUBE_SCANNER}") {
                        sh '''
                            # Setup virtual environment using ShiningPanda
                            python -m venv venv
                            source venv/bin/activate
                            pip install -r requirements.txt
                            pip install pytest pytest-cov
                            pytest --cov=app.py --cov-report=xml
                            sonar-scanner
                        '''
                    }
                }
            }
        }

        stage('Wait for Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build') {
            when {
                expression {
                    currentBuild.resultIsBetterOrEqualTo('SUCCESS')
                }
            }
            steps {
                echo "Build step - all good from quality gate"
                sh 'echo "Packaging app..."'
            }
        }

        stage('Deploy') {
            when {
                expression {
                    currentBuild.resultIsBetterOrEqualTo('SUCCESS')
                }
            }
            steps {
                echo "Deploying the app..."
                sh 'echo "Deploying Flask app..."'
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed. Please check the logs!"
        }
    }
}
