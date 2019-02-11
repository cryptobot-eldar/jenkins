pipeline {
    agent any
    parameters {
        string(name: 'UPGRADE', defaultValue: 'true')
        string(name: 'NAMESPACE', defaultValue: 'crypto')
    }
    stages {
     //   stage('Docker image Generator'){
     //       steps {
     //           script {
     //               def app
     //               
     //               echo 'Building image...'
     //               app = docker.build("egaraev/trading:${env.BUILD_NUMBER}")
                    
     //               echo 'Testing image...'
     //               app.inside {
     //                   sh 'echo "Tests passed"'
     //               }
                    
     //               echo 'Pushing Image...'
     //               docker.withRegistry() {
     //                   app.push("${env.BUILD_NUMBER}")
     //                   //app.push("latest")
     //               }
     //           }
     //       }
     //   }
        stage('Get-Repos') {
            steps {
                echo '################################################################################################'
                echo 'DOWNLOADING CRYPTOBOT REPO...'
                dir('cryptobot') {
                    git branch: 'master',
                        credentialsId: 'egaraev',
                        url: 'https://gitlab.com/cryptobot-group/devops.git'
                }
                sh('ls cryptobot')
            }
        }
        stage('Kubectl') {
            steps {
                echo '################################################################################################'
                /* using the custom image is not necessary
                echo 'Installing kubectl...'
                sh 'curl -L https://dl.k8s.io/v1.10.6/bin/linux/amd64/kubectl -o /tmp/files/kubectl'
                sh 'chmod 755 /tmp/files/kubectl'
                sh 'PATH=$PATH:/tmp/files' */
                sh 'kubectl -h > /dev/null'
            }
        }
        stage ('ASK'){
            options {
                timeout(time: 1, unit: 'MINUTES') 
            }
            input {
                message "Do you want to Deploy?"
            }
            steps{
                echo "Starting to deploy..."
            }
        }
        stage('Deploy') {
            steps {
                echo '################################################################################################'
                echo 'Creatin KUBECONFIG File...'
                withCredentials([kubeconfigContent(credentialsId: 'd82f81dd-665a-4a4e-bb2b-d81413a82759', variable: 'KUBECONFIG_CONTENT')]) {
                    sh '''set +x && echo "$KUBECONFIG_CONTENT" > kube.conf'''
                }
                echo 'STARTING DEPLOY'
                sh 'bash cryptobot/deploy-jenkins.sh'
            }
        }
    }
    post {
        success {
            slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'")
        }
        failure {
            slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'")
        }
	}
    
}


