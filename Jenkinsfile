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
                    dockerPush()

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