node{
    def buildNumber = BUILD_NUMBER
    stage("Git CheckOut"){
        git url: 'https://github.com/FlorenceNway/java_web_app_docker.git',branch: 'project2swarm'
    }
    
    stage("Maven Clean Package"){
        
        def mavenHome = tool name: "Maven", type: "maven"
            sh"${mavenHome}/bin/mvn clean package"
    }
    stage("Build Docker Image") {
         sh "docker build -t flonway/java-web-app:${buildNumber} ."
    }
    stage("Docker login And Push"){
        
        withCredentials([string(credentialsId: 'dockerhubPwd', variable: 'dockerhubPwd')]) {
            sh"docker login -u flonway -p ${dockerhubPwd}"
        }
        sh "docker push flonway/java-web-app:${buildNumber}"
    }
    stage("Remove Local Image"){
        sh "docker rmi -f flonway/java-web-app:${buildNumber}"
    }
    stage("Deploy to docker swarm cluster"){
        sshagent(['ssh_srv']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.86.146 docker service rm javawebapp || true'
            sh "ssh ubuntu@172.31.86.146 docker service create --name javawebapp -p 8080:8080 --replicas 3 flonway/java-web-app:${buildNumber}"
        }
    }
}
