pipeline {
    agent any

    tools {
        maven 'Maven 3.8' // Configure in Jenkins Global Tool Configuration
        jdk 'JDK 17'      // Configure in Jenkins Global Tool Configuration
    }

    environment {
        // Optional: define number of workers for parallel execution
        WORKERS = '2'
    }

    stages {
        stage('Install Playwright Browsers') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'mvn exec:java -e -D exec.mainClass="com.microsoft.playwright.CLI" -D exec.args="install --with-deps"'
                    } else {
                        bat 'mvn exec:java -e -D exec.mainClass="com.microsoft.playwright.CLI" -D exec.args="install --with-deps"'
                    }
                }
            }
        }
        
        stage('Test Execution') {
            steps {
                script {
                    if (isUnix()) {
                        sh "mvn clean test -Dworkers=${env.WORKERS}"
                    } else {
                        bat "mvn clean test -Dworkers=${env.WORKERS}"
                    }
                }
            }
        }
    }

    post {
        always {
            // 1. Publish Allure Report (Requires Allure Jenkins Plugin)
            allure includeProperties: false, results: [[path: 'target/allure-results']]
            
            // 2. Publish Standard JUnit Reports
            junit 'target/surefire-reports/*.xml'
            
            // 3. Archive Test Artifacts (Videos, Screenshots, Traces)
            archiveArtifacts artifacts: 'target/videos/**/*, target/screenshots/**/*, target/traces/**/*', allowEmptyArchive: true
        }
    }
}
