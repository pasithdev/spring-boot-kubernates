pipeline{
    agent any
    environment {
        MAVEN_ARGS = " -e clean install"
    }
    stages{
        stage('Build Maven Project'){
            steps{
                withMaven(maven: "MAVEN_HOME"){
                    sh "mvn ${MAVEN_ARGS}"
                }
            }
        }
        stage('Run Spring Boot Application'){
            steps{
                withEnv(["PATH=/usr/local/bin:$PATH", "JENKINS_NODE_COOKIE=do_not_kill"]){
                    sh "nohup java -jar target/demo-0.0.1-SNAPSHOT.jar &"
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