pipeline {

    agent {
        label "bulid_node"
    }

    stages {
        stage ('SCM') {
            steps {
                echo "hello"
                git 'https://github.com/rehan797/Java_web_app.git'
            }
        }


        stage ('Code Anaylsis') {
            steps {
                script{
                    withSonarQubeEnv('sonar') {
                     sh 'mvn sonar:sonar'
                    }
                }
            }
        }
        

        stage ('Build by maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage ('Docker image') {
            steps {
                sh "sudo docker build -t rehan797/javaimage:${BUILD_NUMBER} ."
            }

        }   

        stage ('Push to docker hub') {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_HUB', variable: 'Docker_password')]) {
    // some block
                sh "sudo docker login -u rehan797 -p $Docker_password"
}
                sh "sudo docker push rehan797/javaimage:${BUILD_NUMBER}"
            }

        }

        stage ('Launching the server in Dev Env') {
            steps {
                sh 'sudo docker rm -f java_App'
                sh "sudo docker run -d -p 8080:8080 --name java_App rehan797/javaimage:${BUILD_NUMBER} "
            }

        } 

        stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['QA_ENV']) {
    
                    sh "ssh  -o  StrictHostKeyChecking=no ubuntu@54.234.34.169 sudo docker rm -f java_App"
                    sh "ssh ubuntu@54.234.34.169 sudo docker run  -d  -p  8080:8080 --name java_App   rehan797/javaimage:${BUILD_NUMBER}"
                }

            }
            
        } 

        stage('QAT Test') {
            steps {               
                
                retry(10) {
                    sh 'curl --silent http://54.234.34.169:8080/java-web-app/ |  grep Welcome'
                }
            
               
            }
        }

        stage ('Approval stage') {
            steps {
               input (message: "Do want to cont.....")
            }
        } 

        stage('Deploy webAPP in PRODUCTION Env') {
            steps {
               
               sshagent(['QA_ENV']) {
    
                    sh "ssh  -o  StrictHostKeyChecking=no ubuntu@3.87.75.15 sudo docker rm -f java_App"
                    sh "ssh ubuntu@3.87.75.15 sudo docker run  -d  -p  8080:8080 --name java_App   rehan797/javaimage:${BUILD_NUMBER}"
                }

            }
            
        } 
    }
}

