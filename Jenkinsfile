pipeline {
    agent any
    parameters {
        string(name: 'tomcat-dev', defaultValue: '18.222.88.228', description: 'Staging server')
        string(name: 'tomcat-prod', defaultValue: '18.217.160.169', description: 'Production server')
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

        stage ('Keys') {
            steps {
                echo "copying keys"
                sh "cp /home/jenkins/tomcat-demo2.pem /tmp/key.pem"
                sh "chmod 600 /tmp/key.pem"
            }
        }

        stage ('Deployments'){

            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp -i /tmp/key.pem **/target/*.war ec2-user@${params.tomcat-dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /tmp/key.pem **/target/*.war ec2-user@${params.tomcat-prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}