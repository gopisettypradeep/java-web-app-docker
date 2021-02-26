node{
    currentBuild.displayName = "${BUILD_NUMBER}"
    stage('SCM Checkout'){
        git url: 'https://github.com/gopisettypradeep/java-web-app-docker.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "3.0.5", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    
    stage('Build Docker Image'){
        sh 'docker build -t gopisettypradeep/java-web-app .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'docker_hubpass', variable: 'dockerpassvar')]) {
          sh "docker login -u gopisettypradeep -p ${dockerpassvar}"
        }
        sh 'docker push gopisettypradeep/java-web-app'
     }
     
      stage('Run Docker Image In Dev Server'){
        
        def dockerRun = ' docker run  -d -p 8080:8080 --name java-web-app gopisettypradeep/java-web-app'
         
         sshagent(['DOCKER_SERVER']) {
          sh 'ssh -o StrictHostKeyChecking=no vagrant@192.168.56.12 uname -a'
          sh sudo 'ssh -o StrictHostKeyChecking=no vagrant@192.168.56.12 docker stop java-web-app || true'
          sh 'ssh vagrant@192.168.56.12 docker rm java-web-app || true'
          sh 'ssh vagrant@192.168.56.12 docker rmi -f  $(docker images -q) || true'
          sh "ssh vagrant@192.168.56.12 ${dockerRun}"
       }
       
    }
     
     
}
