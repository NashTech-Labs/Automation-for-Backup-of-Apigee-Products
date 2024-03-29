import java.text.SimpleDateFormat

pipeline {
    agent any
    environment {
          GCLOUD_DIR = "$JENKINS_HOME/google-cloud-sdk/bin"
          APIGEE_CLI_DIR = "$HOME/.apigeecli/bin"
          GITHUB_CREDS = "<GITHUB_CREDS_ID>"
    }
    stages {
        stage('Installing Dependencies') {
            steps {
                sh '''#!/bin/bash
                    echo "Checking for pre-installed dependencies..."
                    echo ""
                    if [ ! -d "$GCLOUD_DIR" ]; then
                        echo "Installing GCloud CLI..."
                        echo ""
                        cd $JENKINS_HOME
                        curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-412.0.0-linux-x86_64.tar.gz
                        tar -xf google-cloud-cli-412.0.0-linux-*.tar.gz
                        ./google-cloud-sdk/install.sh -q
                        source $JENKINS_HOME/google-cloud-sdk/completion.bash.inc
                        source $JENKINS_HOME/google-cloud-sdk/path.bash.inc
                    else
                        echo "GCloud CLI is already Installed!"
                        echo ""
                    fi

                    if [ ! -d "$APIGEE_CLI_DIR" ]; then
                        echo "Installing Apigee CLI..."
                        echo ""
                        curl -L https://raw.githubusercontent.com/apigee/apigeecli/main/downloadLatest.sh | sh -
                    else
                        echo "Apigee CLI is already Installed!"
                        echo ""
                    fi
                '''
            }
        }
        stage('Logging into Google Cloud and Get Access Token') {
            steps {
                script {
                    withCredentials([file(credentialsId: '<GCP_CREDENTIAL_ID>', variable: 'GOOGLE_SERVICE_ACCOUNT_KEY')]) {
                        sh "${GCLOUD_DIR}/gcloud auth activate-service-account --key-file ${GOOGLE_SERVICE_ACCOUNT_KEY}"
                        env.TOKEN = sh([script: "${GCLOUD_DIR}/gcloud auth print-access-token", returnStdout: true]).trim()
                    }
                }
            }
        }
        stage("Clone Git Repository") {
            steps {
                git(
                    url: "https://github.com/<GITHUB_USER_NAME>/Apigee.git",
                    credentialsId: "${GITHUB_CREDS}",
                    branch: "main",
                    changelog: true,
                    poll: true
                )
            }
        }
        stage("Exporting products from Apigee") {
            steps {
            script {
            def currentDate = new Date().format("dd-MM-yyyy")
                    def exportDir = "<APIGEE_ORG_NAME>/${currentDate}/products"
                    
                    sh """
                    mkdir -p ${exportDir}
                    ${APIGEE_CLI_DIR}/apigeecli products export -o <APIGEE_ORG_NAME> -t ${TOKEN} 
                    """
                    sh "mv products.json ${exportDir}"
                    sh "git config --global user.name '<GITHUB_USER_NAME>'"
                    sh "git config --global user.email '<GITHUB_USER_EMAIL>'"
                    sh "git add ."
                    
            def changes = sh(script: "git status --porcelain", returnStdout: true).trim()
            if (changes) {
                sh "git commit -m 'Added products from Jenkins Pipeline'"
                withCredentials([gitUsernamePassword(credentialsId: "${GITHUB_CREDS}", gitToolName: 'Default')]) {
                    sh "git push -u origin main"
                }
            } else {
                echo "No changes to commit in products. Skipping git push."
                    }
                }
            }
        }
      
    post {
        always {
            deleteDir()
        }
    }

}
