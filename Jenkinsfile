pipeline {
    agent any
    environment {
        CODE_REVISION = ''
    }
    stages {
        stage('Set Code Revision') {
            steps {
                script {
                    // Fetch the code revision from 'images.txt'
                    def revision = sh(script: '''
                        export ARGOCD_SERVER=${ARGOCD_NP_SERVER}
                        export ARGOCD_AUTH_TOKEN="${argotoken}"
                        apt update
                        apt install -y jq
                        argocd app get my-app -o json | jq -r '.status.summary.images[]' >> images.txt
                        cat images.txt
                        head -n 1 "images.txt" | cut -d ':' -f2
                    ''', returnStdout: true).trim()
                    // Assign the fetched revision to the environment variable
                    env.CODE_REVISION = revision
                }
            }
        }
        stage('Use Code Revision') {
            steps {
                echo "The existing code revision is: ${env.CODE_REVISION}"
            }
        }
    }
}