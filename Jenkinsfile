node{
     
    stage("schem chekout"){
      git credentialsId: 'github-crid', url: 'https://github.com/pratikinamake/sfgds.git',branch: 'uat'
    }
	stage("mvn install"){
      sh 'mvn clean package'
    }
	 stage("build docker image"){
       sh "docker build -t $image_name ."    
    }
	stage("push docker image"){
	docker.withRegistry('http://002074205979.dkr.ecr.us-east-2.amazonaws.com', 'ecr:us-east-2:aws-ecr') {
	docker.image('test-test').push('latest')
            }
	
       }
  sshagent(['client-2']) {
	 sh 'ssh -t -t ec2-user@172.31.22.70 -o StrictHostKeyChecking=no "docker ps -q --filter ancestor="$image_name" | xargs -r docker stop"'
	 sh 'ssh -t -t ec2-user@172.31.22.70 -o StrictHostKeyChecking=no "docker container prune -f"'
	 sh 'ssh -t -t ec2-user@172.31.22.70 -o StrictHostKeyChecking=no "eval $(aws ecr get-login --no-include-email --region eu-east-2)"'
     	 sh 'ssh -t -t ec2-user@172.31.22.70 -o StrictHostKeyChecking=no "docker pull 002074205979.dkr.ecr.us-east-2.amazonaws.com/test-test"'
   	 sh 'ssh -t -t ec2-user@172.31.22.70 -o StrictHostKeyChecking=no "docker image tag 002074205979.dkr.ecr.us-east-2.amazonaws.com/test-test $image_name"'
         sh 'ssh -t -t ec2-user@172.31.22.70 -o StrictHostKeyChecking=no  "docker run -d -p 8080:8080 $image_name"' 
         sh 'ssh -t -t ec2-user@172.31.22.70 -o StrictHostKeyChecking=no "docker images -qf dangling=true | xargs docker rmi"'
	 sh 'ssh -t -t ec2-user@172.31.22.70 -o StrictHostKeyChecking=no "docker rmi 002074205979.dkr.ecr.us-east-2.amazonaws.com/test-test "'

        
               }	             
	}	
