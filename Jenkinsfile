pipeline {
    agent any
    tools { 
        maven 'Maven'
        jdk 'JDK'
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "JAVA_HOME = ${JAVA_HOME}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
       
        stage('SCA') {
          steps {
            dependencyCheck additionalArguments: ''' 
                    -o './'
                    -s './'
                    -f 'ALL' 
                    --prettyPrint''', odcInstallation: 'OWASP Dependency-Check Vulnerabilities'
        
            dependencyCheckPublisher pattern: 'dependency-check-report.xml'
          }
        }
     
        stage ('SAST') {
          steps {
            withSonarQubeEnv('SonarQube') {
              sh 'mvn sonar:sonar'
              sh 'cat target/sonar/report-task.txt'
            }
          }
        }

        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage ('Deploy-To-Tomcat') {
            steps {
            sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war student@172.16.0.11:/opt/tomcat/apache-tomcat-9.0.75/webapps/webapp.war'
            }      
         }       
      }
        stage ('DAST') {
            steps {
            sshagent(['zap']) {
               sh 'ssh -o  StrictHostKeyChecking=no student@172.16.1.52 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://172.16.0.11:8080/webapp/" || true'
            }
         }
      }
    }
}
