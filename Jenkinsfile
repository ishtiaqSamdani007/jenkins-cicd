pipeline{
    // agent{
    //     label "node"
    // }

    agent any

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
                sh "docker build -t ghcr.io/ishtiaqsamdani007/jenkins-react-app:${env.BUILD_NUMBER} ."
            }
        }

        stage("push docker image to ghcr"){
            steps{
                echo "pushing docker image to ghcr"
                sh "docker push ghcr.io/ishtiaqsamdani007/jenkins-react-app:${env.BUILD_NUMBER}"
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