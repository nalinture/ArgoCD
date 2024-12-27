pipeline {
    agent any

    stages {
        stage('Fetch existing Image ID for rollback') {
            steps {
                script {
                    def DEFINITION = readYaml file: 'delivery definition.yml'
                    echo "DDF: ${DEFINITION}"

                    // Set environment variables
                    env.ARGOCD_NP_SERVER = "${DEFINITION.JENKINS.argocd_server_url_np}"
                    env.ARGOCD_DEV_TOKEN = credentials("${DEFINITION.JENKINS.argocd_dev_token}")

                    container('argocd') {
                        withCredentials([usernamePassword(credentialsId: "${DEFINITION.JENKINS.argocd_dev_token}", 
                                                          passwordVariable: 'ARGOCD_AUTH_TOKEN')]) {
                            // Run the .sh file and capture the output
                            def codeRevision = sh(
                                script: """
                                export ARGOCD_SERVER=${env.ARGOCD_NP_SERVER}
                                export ARGOCD_AUTH_TOKEN=${env.ARGOCD_AUTH_TOKEN}
                                ./fetch_image_id.sh
                                """, 
                                returnStdout: true
                            ).trim()

                            // Assign the output to an environment variable for use in next stages
                            env.CODE_REVISION = codeRevision
                            echo "The fetched Code Revision is: ${env.CODE_REVISION}"
                        }
                    }
                }
            }
        }

        stage('Use Code Revision in Next Stage') {
            steps {
                script {
                    echo "Using Code Revision: ${env.CODE_REVISION}"
                    // You can use env.CODE_REVISION in your logic here
                }
            }
        }
    }
}