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
                docker_image = "mvmadhan/tomcat:${BUILD_NUMBER}"
            }
            steps {
                sh 'docker build -t ${docker_image} .'
            }
        }
        stage{
            environment{
                DOCKER_CRED = credentials("docker-cred")
            }
            steps{
                docker.withRegistry('https://index.docker.io/v1/', "docker-cred")
                docker.image("${docker_image}:${BUILD_NUMBER}").PUSH()
            }
        }
        
    }

}
