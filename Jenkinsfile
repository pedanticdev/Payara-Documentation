

pipeline {
    agent {
        label 'documentation'
    }
    environment {
        ANTORA_VERSION = 'antora/antora:3.1.9'
    }
    stages {

        stage('Enterprise Documentation Preview') {
            steps {
                script {
                    // Ensure Docker is running
                    sh "sudo systemctl start docker || true"
                    // Run Antora for enterprise-local-playbook.yml
                    sh """
                    sudo docker run --entrypoint antora -v ${WORKSPACE}:/antora -u 1000 --rm -t ${env.ANTORA_VERSION} generate --stacktrace enterprise-local-playbook.yml
                    """
                }
            }
        }

        stage('Archive and Publish Generated Docs') {
            steps {
                // Archive community and enterprise docs as Jenkins artifacts
                archiveArtifacts artifacts: 'build/enterprise/html/**', allowEmptyArchive: true

                // Publish HTML reports
                publishHTML([
                    reportName: 'Enterprise Documentation',
                    reportDir: 'build/enterprise/html',
                    reportFiles: 'index.html',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
    }

    post {
        success {
            echo 'Documentation previews generated and archived successfully.'
        }
        failure {
            echo 'Failed to generate documentation previews.'
        }
    }
}
