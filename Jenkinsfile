library identifier: 'RHTAP_Jenkins@main', retriever: modernSCM(
  [$class: 'GitSCMSource',
   remote: 'https://github.com/redhat-appstudio/tssc-sample-jenkins.git'])

pipeline {
    agent {
        kubernetes {
            label 'jenkins-agent'
            cloud 'openshift'
            serviceAccount 'jenkins'
            podRetention onFailure()
            idleMinutes '30'
            containerTemplate {
                name 'jnlp'
                image 'image-registry.openshift-image-registry.svc:5000/jenkins/jenkins-agent-base@sha256:495c6ba97ec4509694a5afcf7c6e1fbaeda570031c1b6d03e0e26fa13b4c8f1e'
                ttyEnabled true
                args '${computer.jnlpmac} ${computer.name}'
            }
        }
    }
    environment {
        // Only COSIGN_PUBLIC_KEY is needed but init.sh will fail otherwise
        COSIGN_SECRET_PASSWORD = credentials('COSIGN_SECRET_PASSWORD')
        COSIGN_SECRET_KEY = credentials('COSIGN_SECRET_KEY')
        COSIGN_PUBLIC_KEY = credentials('COSIGN_PUBLIC_KEY')
    }
    stages {
        stage('Compute Image Changes') {
            steps {
                script {
                    rhtap.info('gather_deploy_images')
                    rhtap.gather_deploy_images()
                }
            }
        }
        stage('Verify EC') {
            steps {
                script {
                    rhtap.info('verify_enterprise_contract')
                    rhtap.verify_enterprise_contract()
                }
            }
        }
    }
}
