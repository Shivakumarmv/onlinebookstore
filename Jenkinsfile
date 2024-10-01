pipeline {
    agent {
        docker {
            image 'mvmadhan/maven-docker-agent:v1'
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    stages {
        stage("checkout") {
            steps {
                sh 'echo file is cloned'
            }
        }
        
        stage("build") {
            steps {
                
                sh 'mvn package'
                sh 'pwd ; ls -la'
            }
        }
        
        
        stage("dockerbuild") {
            environment {
                docker_image = "mvmadhan/tomcat"
                DOCKER_CRED = credentials("docker-cred")
            }
            steps {
                script{
                    sh'docker build -t ${docker_image}:${BUILD_NUMBER}  .'
                    def dockerImage = docker.image("${docker_image}:${BUILD_NUMBER}")
                    docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                        dockerImage.push()
                        }
                }
            }
        }
        stage('Update Deployment File') {
            environment {
                GIT_REPO_NAME = "onlinebookstore"
                GIT_USER_NAME = "Shivakumarmv"
            }
            steps {
                withCredentials([string(credentialsId: 'github-cred', variable: 'GITHUB_TOKEN')]) {
                    sh '''
                    git config user.email "madhanshiva.xyz@gmail.com"
                    git config user.name "madhan shiva"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" onlinebookstore-manifests/dep-svc.yml
                    git add onlinebookstore-manifests/dep-svc.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    '''
                }
            }
        }
    }
}
