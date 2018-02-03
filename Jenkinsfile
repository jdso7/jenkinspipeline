pipeline {
    agent any
    tools { 
        maven 'localMaven' 
        jdk 'localJDK' 
    }

    parameters {
         string(name: 'tomcat_dev', defaultValue: '13.58.18.73', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.216.91.238', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }
stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/jenkins/tomcat-demo.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}
