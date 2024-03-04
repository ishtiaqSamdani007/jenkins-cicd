pipeline{
    // agent{
    //     label "node"
    // }

    agent any

    tools{
        nodejs "node"
    }

    environment{
        PUBLIC_IP = "34.16.140.202"
    }

    stages{
        stage("install react packagees"){
            steps{
                echo "installing react packages"
                sh "npm install"
            }
         
        }

        stage("build react app"){
            steps{
                echo "building react app"
                sh "npm run build"
            }
        }

        stage("login to ghcr"){
            steps{
                echo "logging into ghcr"
                withCredentials([usernamePassword(credentialsId: 'ghcr-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
                    sh "docker login ghcr.io -u $USERNAME -p $PASSWORD"
                }
            }
        }

        stage("dockerize react app"){
            steps{
                echo "dockerizing react app"
                sh "docker build -t ghcr.io/ishtiaqsamdani007/jenkins-react-app:v${env.BUILD_NUMBER} ."
            }
        }

        stage("push docker image to ghcr"){
            steps{
                echo "pushing docker image to ghcr"
                sh "docker push ghcr.io/ishtiaqsamdani007/jenkins-react-app:v${env.BUILD_NUMBER}"
            }
        }

        stage("remove docker image from local"){
            steps{
                echo "removing docker image from local"
                sh "docker rmi ghcr.io/ishtiaqsamdani007/jenkins-react-app:v${env.BUILD_NUMBER}"
            }
        }
// #############################

        stage("ghcr login in vm through ssh"){
            steps{
                echo "logging into vm through ssh"
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-creds', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]){
                    withCredentials([usernamePassword(credentialsId: 'ghcr-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]){
                    sh "ssh -o StrictHostKeyChecking=no -i $SSH_KEY $SSH_USER@$PUBLIC_IP 'docker login ghcr.io -u $USERNAME -p $PASSWORD'"
                }

                    
                }
            }
        }

        stage("run docker in vm through ssh and remove previous container"){
            steps{
                echo "running in vm through ssh"
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-creds', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]){
                    sh ```
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY $SSH_USER@$PUBLIC_IP '
                        docker stop react-app || true
                        docker rm react-app || true
                        docker run -d -p 80:80 --name react-app ghcr.io/ishtiaqsamdani007/jenkins-react-app:v${env.BUILD_NUMBER}
                        '

                    ```

                }
            }
        }
                    

    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}