pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER = 'sonar' // Your SonarQube installation name in Jenkins
    }

    stages {

        stage('Quality Gate') {
            steps {
                script {
                    withSonarQubeEnv("${env.SONARQUBE_SCANNER}") {
                        sh '''
                            # Ensure python3 and pip3 are available
                            python3 --version
                            pip3 --version

                            # Install dependencies
                            pip3 install -r requirements.txt
                            pip3 install pytest pytest-cov

                            # Run unit tests and generate coverage report
                            pytest --cov=app.py --cov-report=xml

                            # Run SonarQube scanner
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
                // Add your build steps here (e.g., docker build, maven package)
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
                // You can add Docker build/push or kubectl apply here
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed. Please check the logs!"
        }
    }
}
