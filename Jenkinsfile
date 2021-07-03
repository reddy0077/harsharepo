pipeline {
    environment {
        registry = '29207/dockerrepo'
        registryCredential = 'Varun_29207'
        dockerSwarmManager = '192.168.0.92:2375'
        dockerhost = '192.168.0.92'
        dockerImage = ''
    }
    agent any
    stages {
        stage('Cloning our Git') {
            steps {
                git 'https://github.com/reddy0077/harsharepo.git'
            }
        }
        stage('Push Image To DockerHUB') {
            steps {
                script {
                    /* groovylint-disable-next-line NestedBlockDepth */
                    docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Cleaning up') {
            steps {
                sh "docker rmi $registry:v$BUILD_NUMBER"
            }
        }
        stage('Deploying to Docker Swarm') {
            steps {
                sh "docker -H tcp://$dockerSwarmManager service rm testing1 || true"
                /* groovylint-disable-next-line LineLength */
                sh "docker -H tcp://$dockerSwarmManager service create --name testing1 -p 8100:80 $registry:v$BUILD_NUMBER"
            }
        }
        stage('Verifying The Deployment') {
            steps {
                sh 'curl http://$dockerhost:8100 || exit 1'
                }
        }
    }
}
