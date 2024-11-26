pipeline {
    agent any

    environment {
        // Snowflake connection details stored in Jenkins credentials
        SNOWFLAKE_ACCOUNT = 'kd54208.ap-south-1.aws' // Snowflake account (can be stored securely)
        SNOWFLAKE_USER = 'Snowflakedbtpoc' // Jenkins credential for Snowflake username
        SNOWFLAKE_PASSWORD = 'Welcome@12345' // Jenkins credential for Snowflake password
        SNOWFLAKE_WAREHOUSE = 'COMPUTE_WH'
        SNOWFLAKE_DB = 'COMMEXEC_QA'
        SNOWFLAKE_SCHEMA = 'DBT_QA'
        DBT_PROFILE = 'my_project' // The profile defined in the profiles.yml
    }

    stages {
        stage('Clone DBT Repository') {
            steps {
                git 'https://github.com/snowflakerama/dbt-poc.git' // Clone your DBT repo
            }
        }

        stage('Install DBT and Dependencies') {
            steps {
                script {
                    // Install DBT and any other required dependencies
                    sh 'pip install python'
                    sh 'pip install --upgrade pip'
                    sh 'pip install dbt-core pip install dbt-snowflake'  
                }
            }
        }
                stage('Run DBT Models') {
            steps {
                script {
                    // Set Snowflake credentials as environment variables for DBT run
                    withEnv([
                        "SNOWFLAKE_ACCOUNT=${SNOWFLAKE_ACCOUNT}",
                        "SNOWFLAKE_USER=${SNOWFLAKE_USER}",
                        "SNOWFLAKE_PASSWORD=${SNOWFLAKE_PASSWORD}",
                        "SNOWFLAKE_WAREHOUSE=${SNOWFLAKE_WAREHOUSE}",
                        "SNOWFLAKE_DB=${SNOWFLAKE_DB}",
                        "SNOWFLAKE_SCHEMA=${SNOWFLAKE_SCHEMA}"
                    ]) {
                        // Run DBT models (this will apply transformations in your Snowflake warehouse)
                        sh 'dbt run --profiles-dir . --target QA'  // Make sure to provide the correct profile target
                    }
                }
            }
        }
                stage('Run DBT Tests') {
            steps {
                script {
                    // Run DBT tests (to validate the integrity of data)
                    sh 'dbt test --profiles-dir . --target QA'
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
                success {
            echo 'DBT pipeline run completed successfully.'
        }

        failure {
            echo 'DBT pipeline run failed.'
        }
    }
}
   
