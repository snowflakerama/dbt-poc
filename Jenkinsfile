pipeline {
    agent any
    environment {
        DBT_SNOWFLAKE_USER = credentials('snowflake-user')
        DBT_SNOWFLAKE_PASSWORD = credentials('snowflake-password')
        DBT_PROFILES_DIR = '.'
    }
    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }
        stage('Setup Environment') {
            steps {
                sh 'python3 -m venv venv'
                sh './venv/bin/pip install --upgrade pip'
                //sh './venv/bin/pip install dbt-core
                sh './venv/bin/pip install dbt-snowflake'
            }
        }
        stage('Run dbt Commands') {
            steps {
                sh '''
                ./venv/bin/dbt deps
                ./venv/bin/dbt seed --target prod
                ./venv/bin/dbt run --target prod
                '''
                //./venv/bin/dbt test --target prod
            }
        }
    }
    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
    }
}
