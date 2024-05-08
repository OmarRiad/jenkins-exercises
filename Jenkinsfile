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
                        sh 'npm version minor'
                        def packageJson = readJSON file:package.json
                        def version = packageJson.version
                        env.IMAGE_NAME = $version-$BUILD_NUMBER
                    }
                }
            }
            
        }
        stage("run tests"){
            steps{
                script{
                    echo "running tests"
                    dir("app"){
                        sh 'npm install'
                        sh 'npm test'
                    }
                }
            }
        }
        stage("build docker image"){
            steps{
                script{
                    echo "building docker image"
                    withCredentials([usernamePassword(credentialsId: "dockerhub-credentials", passwordVariable: "PASS", usernameVariable:"USER")]){
                    sh "docker build -t omarriad07/demo-app:${IMAGE_NAME} ."
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh "docker push omarriad07/demo-app:${IMAGE_NAME}"
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