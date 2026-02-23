pipeline {
    agent any

    triggers {
        // Every 5 minutes on Mondays
        cron('*/5 * * * 1')
    }

    options {
        timestamps()
    }

    environment {
        MVNW = "mvnw.cmd"
        MAVEN_REPO = "${WORKSPACE}\\.m2repo"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat "${env.MVNW} -B -Dmaven.repo.local=${env.MAVEN_REPO} -DskipTests -Dcheckstyle.skip=true clean package"
            }
        }

        stage('Test + Coverage') {
            steps {
                bat "${env.MVNW} -B -Dmaven.repo.local=${env.MAVEN_REPO} -Dcheckstyle.skip=true test"
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            junit testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true
        }
    }
}
