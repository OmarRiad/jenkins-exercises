@Library('jenkinsEx-shared-library') _
pipeline{
    agent any
    tools{
        nodejs "nodejs"
    }
    stages{
        stage("increment version"){
            steps{
                script{
                    echo "incrementing app version"
                    dir("app"){
                        IncrementV()
                    }
                }
            }
            
        }
        stage("run tests"){
            steps{
                script{
                    echo "running tests"
                    dir("app"){
                        runTests()
                    }
                }
            }
        }
        stage("build docker image"){
            steps{
                script{
                    echo "building docker image"
                    dockerLogin()
                    dockerBuild("omarriad07/demo-app")
                    dockerPush("omarriad07/demo-app")

            }
          }
        }
        stage('deploy to EC2') {
            steps {
                script {
                   def shellCmd = "bash ./server-cmds.sh omarriad07/demo-app:${IMAGE_NAME}"
                   def ec2Instance = "ec2-user@13.39.50.216"

                   sshagent(['ec2-server-key']) {
                       sh "scp -o StrictHostKeyChecking=no server-cmds.sh ${ec2Instance}:/home/ec2-user"
                       sh "scp -o StrictHostKeyChecking=no docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                       sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
                   }     
                }
            }
        }

        stage("commit version update"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_API_TOKEN')]){
                        setUser()
                        gitPush()
                    }
                }
            }
        }
    }
}