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
        MAVEN_REPO = "${env.WORKSPACE}\\.m2repo"
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

        stage('Test + JaCoCo Coverage') {
            steps {
                bat """
                ${env.MVNW} -B ^
                -Dmaven.repo.local=${env.MAVEN_REPO} ^
                -Dcheckstyle.skip=true ^
                org.jacoco:jacoco-maven-plugin:prepare-agent ^
                test ^
                org.jacoco:jacoco-maven-plugin:report
                """
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                archiveArtifacts artifacts: 'target/site/jacoco/**/*', fingerprint: true
            }
        }
    }

    post {
        always {
            junit testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true
        }
    }
}
