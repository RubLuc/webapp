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

        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage ('Deploy-To-Tomcat') {
            steps {
            sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war student@192.168.243.39:/prod/apache-tomcat-9.0.75/webapps/webapp.war'
            }      
         }       
      }
    }
}
