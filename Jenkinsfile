pipeline {
    agent any

    tools {
        maven 'default'
    }

    parameters {
        booleanParam(
            name: 'SKIP_TESTS',
            defaultValue: false,
            description: 'Skip test execution'
        )
    }

    stages {
        stage('Get') {
            steps {
                checkout scm
                sh 'java -version'
                sh 'mvn -version'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn -B clean compile'
            }
        }

        stage('Test') {
            when {
                expression {
                    !params.SKIP_TESTS
                }
            }
            steps {
                sh 'mvn -B test'
            }
            post {
                always {
                    junit(
                        testResults: 'target/surefire-reports/*.xml',
                        allowEmptyResults: true
                    )
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    if (params.SKIP_TESTS) {
                        sh 'mvn -B package -DskipTests'
                    } else {
                        sh 'mvn -B package'
                    }
                }
            }
        }

        stage('Publish') {
            steps {
                archiveArtifacts(
                    artifacts: 'target/*.jar',
                    fingerprint: true
                )
            }
        }
    }

    post {
        success {
            echo 'Maven pipeline completed successfully.'
        }
        failure {
            echo 'Maven pipeline failed. Check Console Output.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}
