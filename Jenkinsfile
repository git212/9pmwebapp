pipeline{
    agent any
    environment{
        PATH = "${PATH}:${tool name: 'maven3', type: 'maven'}/bin"
    }
    stages{
        stage('SCM Checkout'){
            steps{
                git credentialsId: 'github',
                    url: 'https://github.com/git212/9pmwebapp',
                    branch: 'master'
            }
        }

        stage('Maven Build'){
            steps{
                sh "mvn clean package" 
            }
        }

        stage('Deploy to Dev'){
            steps{
                sshagent(['tomcat-dev']) {
                    // stop tomcat
                    sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.3.47 /opt/tomcat8/bin/shutdown.sh"
                    // copy war file to remote tomcat
                    sh "scp target/9pmwebapp.war ec2-user@172.31.3.47:/opt/tomcat8/webapps/"
                    // start tomcat
                    sh "ssh ec2-user@172.31.3.47 /opt/tomcat8/bin/startup.sh"
                } 
            }
        }
    }
    post{
        success{
            mail body: """Hi Team, the app is successfully deployed
            ${BUILD_URL}

Thanks,
DevOps Team.
Java Home""", subject: "${JOB_NAME}successfully Deployed", to: 'mahantabharat312@gmail.com'
        }

        failure {
            mail body: """Hi Team, the app is deployment failed
            ${BUILD_URL}

Thanks,
DevOps Team.
Java Home""", subject: "${JOB_NAME} - Deployment failed", to: 'mahantabharat312@gmail.com'
        }
    }

}  