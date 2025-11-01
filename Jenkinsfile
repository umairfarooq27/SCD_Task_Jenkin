pipeline {
    agent any
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    
    environment {
        TRIGGERED_BY = "${env.BUILD_CAUSE ==~ /.*GitHub.*/ ? 'GitHub Webhook' : 'Manual'}"
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                script {
                    def timestamp = new Date().format('yyyy-MM-dd HH:mm:ss')
                    echo "Building branch: ${env.BRANCH_NAME ?: 'main'}"
                    echo "Build #${BUILD_NUMBER} triggered by ${TRIGGERED_BY} at ${timestamp}"
                    if (env.BUILD_CAUSE ==~ /.*GitHub.*/) {
                        echo "Triggered by GitHub Webhook"
                    }
                }
                git branch: "${env.BRANCH_NAME ?: 'main'}", 
                     url: 'https://github.com/umairfarooq27/SCD_Task_Jenkin.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }
        
        stage('Run Tests and Linting') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        bat 'npm test'
                    }
                }
                stage('Linting') {
                    steps {
                        bat 'npm run lint'
                    }
                }
            }
        }
        
        stage('Build') {
            steps {
                bat 'npm run build'
            }
        }
        
        stage('Conditional Deployment') {
            steps {
                script {
                    def branchName = env.BRANCH_NAME ?: 'main'
                    if (branchName == 'main') {
                        echo "Deployed to Production Environment (main branch)"
                    } else if (branchName == 'dev') {
                        echo "Deployed to Staging Environment (dev branch)"
                    } else {
                        echo "Feature branch detected – Deployment Skipped."
                    }
                }
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                script {
                    // Create a build artifact archive
                    bat '''
                        if not exist build-output mkdir build-output
                        echo Build artifacts and test results archived. > build-output\\build-info.txt
                    '''
                    archiveArtifacts artifacts: 'build-output/**/*', 
                                   allowEmptyArchive: true, 
                                   fingerprint: true
                }
            }
        }
    }
    
    post {
        success {
            script {
                def timestamp = new Date().format('yyyy-MM-dd HH:mm:ss')
                echo "Build #${BUILD_NUMBER} on branch ${env.BRANCH_NAME ?: 'main'} completed successfully at ${timestamp}"
            }
            echo "Build and tests successful!"
            echo "Email sent to team@example.com"
        }
        failure {
            echo "Build failed or tests failed!"
            echo "Email sent to team@example.com"
        }
        always {
            echo "Build and tests completed!"
        }
    }
}