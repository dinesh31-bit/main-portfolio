properties([pipelineTriggers([githubPush()])])
node {
    def nginx_ip="43.205.96.96"
    try{
        stage("git clone"){
             git branch: 'main', credentialsId: 'dinesh31bitgit', url: 'https://github.com/dinesh31-bit/main-portfolio.git'
        }
        stage("upload to nginx server"){
            sshagent(['nginx-token']) {
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${nginx_ip} sudo systemctl stop nginx"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${nginx_ip} sudo rm -rf /usr/share/nginx/html/*"
                sh "scp -o StrictHostKeyChecking=no * ec2-user@${nginx_ip}:/home/ec2-user/dinesh/"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${nginx_ip} sudo mv /home/ec2-user/dinesh/* /usr/share/nginx/html/"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@${nginx_ip} sudo systemctl start nginx"
                
            }
        }
    }
    catch(Exception e){
          currentBuild.result = 'FAILURE'
    }
    finally{
        def buildstatus = currentBuild.result ?:'success'
        def colorcode='good'
        if(buildstatus == "FAILURE"){
            colorcode='DANGER'
        }
        slackSend channel: 'devops-notification',color: "${colorcode}", message:  "${env.JOB_NAME}-${buildstatus}-${env.BUILD_NUMBER}. Check your jod here:${env.BUILD_URL}"
    }
}