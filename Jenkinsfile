pipeline {
        // prameters for images on docker hub
        parameters {
        string name: 'ORIGINAL_IMAGE', defaultValue: 'alpine/helm:3.1.0', description: 'Complete image name to pull.', trim: true
        string name: 'TAG', defaultValue: 'helm-3.1.0', description: 'New tag in our repo.', trim: true
    }
    
    environment {
        ORIGINAL_IMAGE = "${params.ORIGINAL_IMAGE}"
        REPOSITORY = "URL-REPO-ON-ECR"
        TAG = "${params.TAG}"
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
    }

    agent {
                label "docker"
          }

    stages {
        stage('Print result of environment setting') {
            environment {
                KUBECONFIG = 'PATH-OF-kubeconfig-file-of-k8s-cluster'
            }
                
            steps {
                container('docker') {
                    withDockerRegistry([credentialsId: 'ecr:eu-central-1:user-that-have-perm-to-access-toecr-repo-and-stord-in-cred-on-jenkins', url: "https://$REPOSITORY"]) {
                            //pull the image from docker hub
                            sh "docker pull $ORIGINAL_IMAGE"
                            // tagged that image with name of new repo on ecr
                            sh "docker tag $ORIGINAL_IMAGE $REPOSITORY:$TAG"
                            // push it to new repo on ecr
                            sh "docker push $REPOSITORY:$TAG"
                        }
                }
            }
        }
    }
}
