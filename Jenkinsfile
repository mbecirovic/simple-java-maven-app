pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
        timestamps()
    }

    tools {
        maven 'Maven-3.9.11'
    }

    parameters {
        booleanParam(name: 'SKIP_TESTS', defaultValue: false, description: 'Skip unit tests?')
    }

    environment {
        JAR_PATH = "target/*.jar"
    }

    stages {

        stage('Build') {
            steps {
                echo "Building JAR package..."
                sh "mvn -B -DskipTests clean package"
            }
        }

        stage('Test') {
            when {
                expression { return !params.SKIP_TESTS }
            }
            steps {
                echo "Running unit tests..."
                sh "mvn test"
            }
        }

        stage('Archive Artifact') {
            steps {
                echo "Archiving built JAR..."
                archiveArtifacts artifacts: "${JAR_PATH}", fingerprint: true
            }
        }

        stage('Deliver') { 
            steps {
                echo "Executing deliver script..."
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        unstable {
            echo "Pipeline marked as UNSTABLE."
        }
        failure {
            echo "Pipeline FAILED!"
        }
        always {
            echo "Cleaning workspace..."
            cleanWs()
        }
    }
}
