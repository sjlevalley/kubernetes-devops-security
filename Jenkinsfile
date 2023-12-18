pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' 
            }
        }   
      stage('Unit Tests') {
            steps {
              sh "mvn test"
            }
            post {
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
            }
        }    
      stage('Docker Build & Test') {
            steps {
              withDockerRegistry([credentialsId: "74c4b410-bc5e-4127-85fe-533751261a71", url: ""]) {
                sh "printenv"

              }
            }
           
        }    
    }
}