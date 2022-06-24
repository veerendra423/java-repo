node{
      def dockerImageName= 'veerendraperugu/javadedockerapp_$JOB_NAME:$BUILD_NUMBER'
      stage('SCM Checkout'){
         git 'https://github.com/veerendra423/java-repo.git'
      }
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'myMaven', type: 'maven'   
         bat "${mvnHome}/bin/mvn clean install jetty:run"
         bat "${mvnHome}/bin/mvn package -Dmaven.test.skip=true"
      }       
     
     stage ('Test'){
         def mvnHome =  tool name: 'myMaven', type: 'maven'    
         bat "${mvnHome}/bin/mvn verify; sleep 3"
      }
      
     stage('Build Docker Image'){         
           bat "docker build -t ${dockerImageName} ."
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'docker_cred', variable: 'dockerPWD')]) {
              bat "docker login -u veerendraperugu -p ${dockerPWD}"
         }
        bat "docker push ${dockerImageName}"
      }
      
    stage('Run Docker Image'){
            def dockerContainerName = 'javadockerapp'
            def changingPermission='sudo chmod +x stopscript.sh'
            def scriptRunner='sudo ./stopscript.sh'           
            def dockerRun= "sudo docker run -p 8082:8080 -d --name ${dockerContainerName} ${dockerImageName}" 
            withCredentials([string(credentialsId: 'deploymentserverpwd', variable: 'dpPWD')]) {
                  bat "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no node1@192.168.160.128" 
                  bat "sshpass -p ${dpPWD} scp -r stopscript.sh node1@192.168.160.129:/home/node1" 
                  bat "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no node1@192.168.160.128 ${changingPermission}"
                  bat "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no node1@192.168.160.128 ${scriptRunner}"
                  bat "sshpass -p ${dpPWD} ssh -o StrictHostKeyChecking=no node1@192.168.160.128 ${dockerRun}"
            }
            
      
      }
      
         
  }
      
