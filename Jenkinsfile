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
                    dockerBuild()
                    dockerPush()

            }
          }
        }
        stage("commit version update"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_API_TOKEN')]){
                        sh 'git config --global user.email "jenkins@example.com"'
                        sh 'git config user.name "jenkins"'
                    
                        
                        sh 'git status'
                        sh 'git branch'
                        sh 'git config --list'
                        sh "git remote set-url origin https://${GITHUB_API_TOKEN}@github.com/OmarRiad/jenkins-exercises.git"
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:main'
                    }
                }
            }
        }
    }
}