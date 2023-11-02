pipeline {
    agent any

    environment {
        NAMESPACE = '<name-space>'
        CRONJOB_NAME = '<cronjob-name>'
        JOB_NAME = "<job-name>"
    }

    stages {
        stage('Trigger Kubernetes CronJob') {
            steps {
                script {
                    withKubeConfig([credentialsId: '<cred-id>', serverUrl: '<server-url>']) {
                        sh "kubectl create job $JOB_NAME --from=cronjob/$CRONJOB_NAME -n $NAMESPACE"
                    }
                }
            }
        }
        
        stage('Wait for Job to Start') {
            steps {
                script {
                    withKubeConfig([credentialsId: '<cred-id>', serverUrl: '<server-url>']) {

                        def waitCounter = 0
                        def MAX_RETRIES = 10

                        // Get the status of the pod associated with the job
                        def podStatus = sh(
                            script: "kubectl get pods -n $NAMESPACE -l job-name=$JOB_NAME -o=jsonpath='{.items[*].status.phase}' | tr -d '%'",
                            returnStdout: true
                        )
                        // Get the name of the pod associated with the job
                        def podName = sh(
                            script: "kubectl get pods -n $NAMESPACE -l job-name=$JOB_NAME --output=jsonpath='{.items[*].metadata.name}' | tr -d '%'",
                            returnStdout: true
                        )

                        while (waitCounter <= MAX_RETRIES) {

                            podStatus = sh(
                                script: "kubectl get pods -n $NAMESPACE -l job-name=$JOB_NAME -o=jsonpath='{.items[*].status.phase}' | tr -d '%'",
                                returnStdout: true
                            )

                            if (podStatus == "Running" ) {
                                echo "Pod is running. Printing logs..."
                                sh "kubectl logs -f $podName -n $NAMESPACE"
                                echo "Successfully synced staging database with the production database"
                                currentBuild.result = 'SUCCESS'
                                break
                            } else if (podStatus == "Succeeded") {
                                echo "Pod Succeeded. Printing logs..."
                                sh "kubectl logs $podName -n $NAMESPACE"
                                echo "Successfully synced staging database with the production database"
                                currentBuild.result = 'SUCCESS'
                                break
                            } else if (podStatus == "Failed") {
                                currentBuild.result = 'FAILURE'
                                error "Pod Failed to Successfully execute"
                            } else {
                                echo "Pod is starting..."
                                sleep(10)
                            }

                            waitCounter++
                        }

                        // If the waiting counter exceeds MAX_RETRIES, mark the build as failure and raise an error
                        if (waitCounter >= MAX_RETRIES + 1){
                            currentBuild.result = 'FAILURE'
                            error "Pod status: ${podStatus}... Exceeded maximum waiting time"
                        }
                    }
                }
            }
        }

    }
}