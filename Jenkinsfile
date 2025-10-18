pipeline {
    agent any  // Run the pipeline on any available Jenkins agent

    environment {
        DOCKER_CREDENTIALS_ID = 'roseaw-dockerhub'
        GITHUB_URL = 'https://github.com/vontrodl/225-lab3-8.git'                                    //<------This github URL
        KUBECONFIG = credentials('vontrodl-225')                                                          //<------Your MiamiID
    }

    stages {

        // -------------------------
        // Stage 1: Checkout Code
        // -------------------------
        stage('Checkout') {
            steps {
                // Pull the latest version of the repository from GitHub (main branch)
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: "${GITHUB_URL}"]]
                ])
            }
        }

        // ---------------------------------------
        // Stage 2: Deploy MongoDB and Mongo Express
        // ---------------------------------------
        stage('Build Mongo Stateful Set') {
            steps {
                script {
                    // IMPORTANT: Ensure the secret file (mongo-secret.yaml) has been applied to the cluster first
                    // You can manually create it using kubectl before running this pipeline
                    // The secret contains MongoDB credentials (username/password) used by both Mongo and Mongo Express

                    // Apply all required Kubernetes manifests in correct order
                    sh 'kubectl apply -f mongo-secret.yaml'       // Secret containing credentials
                    sh 'kubectl apply -f mongo.yaml'              // MongoDB StatefulSet or Deployment
                    sh 'kubectl apply -f mongo-configmap.yaml'    // ConfigMap for Mongo initialization parameters
                    sh 'kubectl apply -f mongo-express.yaml'      // Mongo Express web interface
                }
            }
        }

        // -------------------------
        // Stage 3: Verify Deployment
        // -------------------------
        stage('Check Kubernetes Cluster') {
            steps {
                script {
                    // Display all running resources (pods, services, deployments, etc.)
                    // This helps confirm that MongoDB and Mongo Express are deployed and running
                    sh "kubectl get all"
                }
            }
        }
    }

    // -------------------------
    // Post-Build Notifications
    // -------------------------
    post {
        success {
            // Send a Slack message when build succeeds
            slackSend(
                color: "good", 
                message: "✅ Build Completed Successfully: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
        unstable {
            // Send a Slack message when the build finishes but is marked unstable
            slackSend(
                color: "warning", 
                message: "⚠️ Build Unstable: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
        failure {
            // Send a Slack message when the build fails
            slackSend(
                color: "danger", 
                message: "❌ Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            )
        }
    }
}
