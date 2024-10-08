pipeline{
    agent any
    environment {
        MAVEN_ARGS = " -e clean install"
        DOCKER_IMAGE ="pasith/springboot-app-jenkins:latest"
        KUBE_CONFIG_PATH = "/Users/pasith/.kube/config"
        KUBE_NAMESPACE = "default"
    }
    stages{
        stage('Build Maven Project'){
            steps{
                withMaven(maven: "MAVEN_HOME"){
                    sh "mvn ${MAVEN_ARGS}"
                }
            }
        }
        //stage('Run Spring Boot Application'){
        //    steps{
        //        withEnv(["PATH=/usr/local/bin:$PATH", "JENKINS_NODE_COOKIE=do_not_kill"]){
        //            sh "nohup java -jar target/demo-0.0.1-SNAPSHOT.jar &"
        //        }
        //    }
        //}
        stage('Build Docker Image'){
            steps{
                withEnv(["PATH=/usr/local/bin:$PATH"]){
                    //create docker image from Dockerfile
                    sh "docker build -t ${DOCKER_IMAGE} ."
                    //delete image if not used
                    sh "docker image prune -f"
                    //create docker container from image and run it
                    sh "docker images"
                }
            }
        }
        stage('Push Docker Image'){
            steps{
                withEnv(["PATH=/usr/local/bin:$PATH"]){
                    withCredentials([string(credentialsId: 'dockerhub-credentials', variable: 'DOCKERHUB_PASSWORD')]){
                        sh "echo ${DOCKERHUB_PASSWORD} | docker login -u pasith --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
        stage('Deploy to Kubernetes'){
            steps{
                withEnv(["PATH=/usr/local/bin:$PATH"]){
                   sh "kubectl --kubeconfig=${KUBE_CONFIG_PATH} apply -f deployment.yaml -n ${KUBE_NAMESPACE}"
                   sh "kubectl --kubeconfig=${KUBE_CONFIG_PATH} rollout status deployment/springboot-app -n ${KUBE_NAMESPACE}"
                }
            }
        }

    }
    post{
        always{
            cleanWs()
        }
        success{
            echo "Build Success!"
        }
        failure{
            echo "Build Failure"
        }
    }
}