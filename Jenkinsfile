pipeline {
    agent any

    environment {
        DBT_PROFILE = "."
        SNOWFLAKE_ACCOUNT = credentials('snowflake_account') // Jenkins credential ID
        SNOWFLAKE_USER = credentials('snowflake_user')       // Jenkins credential ID
        SNOWFLAKE_PASSWORD = credentials('snowflake_password') // Jenkins credential ID
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the dbt project repository
                git branch: 'master', url: 'https://github.com/snowflakerama/dbt-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install dbt
                sh 'pip install dbt-core dbt-snowflake'
            }
        }

        stage('Run dbt') {
            steps {
                // Run dbt commands
                sh '''
                export SNOWFLAKE_ACCOUNT=$SNOWFLAKE_ACCOUNT
                export SNOWFLAKE_USER=$SNOWFLAKE_USER
                export SNOWFLAKE_PASSWORD=$SNOWFLAKE_PASSWORD

                dbt deps       # Install dbt packages
                dbt seed       # Load seed data
                dbt run        # Build models
                dbt test       # Run tests
                '''
            }
        }
    }

    post {
        always {
            // Clean up workspace
            cleanWs()
        }
        failure {
            echo "Build failed!"
        }
        success {
            echo "Build succeeded!"
        }
    }
}

