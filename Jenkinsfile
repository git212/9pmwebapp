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
                    sh "ssh ec2-user@172.31.3.47 /opt/tomcat8/bin/shutdown.sh"
                    // copy war file to remote tomcat
                    sh "scp target/9pmwebapp.war ec2-user@172.31.3.47:/opt/tomcat8/webapp/"
                    // start tomcat
                    sh "ssh ec2-user@172.31.3.47 /opt/tomcat8/bin/startup.sh"
                } 
            }
        }
    }
}