pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
               git branch: 'main', credentialsId: 'git-token', url: 'https://github.com/Nishanc07/Capstone-Dotnet-manodb-CD.git'
            }
        }
        
        stage('Deploy To Kubernetes') {
            steps {
                script {
                    withKubeConfig(
  caCertificate: '',
  clusterName: 'azure-aks',
  contextName: '',
  credentialsId: 'k8s-token',
  namespace: 'webapps',
  restrictKubeConfigAccess: false,
  serverUrl: 'https://xyz-v73qd8ss.hcp.northeurope.azmk8s.io'
) {
    sh 'kubectl apply --validate=false -f Manifest/manifest.yaml -n webapps'
    sh 'kubectl apply --validate=false -f Manifest/ci.yaml -n webapps'
    sh 'kubectl apply --validate=false -f Manifest/ingress.yaml -n webapps'
    sleep 30
}

                }
            }
        }

        stage('Verify The Deployment') {
            steps {
                script {
                      withKubeConfig(
  caCertificate: '',
  clusterName: 'azure-aks',
  contextName: '',
  credentialsId: 'k8s-token',
  namespace: 'webapps',
  restrictKubeConfigAccess: false,
  serverUrl: 'https://xyz-v73qd8ss.hcp.northeurope.azmk8s.io'
){
                        sh 'kubectl get pods -n webapps'
                        sh 'kubectl get svc -n webapps'
                        sh 'kubectl get ingress -n webapps'
                        sleep 30
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
                def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'

                def body = """
                    <html>
                    <body>
                    <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                    <h2>${jobName} - Build ${buildNumber}</h2>
                    <div style="background-color: ${bannerColor}; padding: 10px;">
                    <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                    </div>
                    <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                    </div>
                    </body>
                    </html>
                """

                emailext (
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                    body: body,
                    to: 'nishanarayan604@gmail.com',
                    from: 'nishanc604@gmail.com',
                    replyTo: 'jenkins@nishanc.com',
                    mimeType: 'text/html'
                )
            }
        }
    }
}


