pipeline {
    agent any

    stages {
        stage('Filter PR Commits') {
            steps {
                script {
                    def commitMessage = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
                    if (commitMessage.contains('skip-ci')) {
                        echo "Skipping build as per commit message."
                        currentBuild.result = 'NOT_BUILT'
                        return
                    }
                }
            }
        }
        stage('Build') {
            when {
                expression { !currentBuild.result.equals('NOT_BUILT') }
            }
            steps {
                echo 'Building...'
                // adding build steps here
            }
        }
    }
}
