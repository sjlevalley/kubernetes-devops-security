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
            // post {
            //   always {
            //     junit 'target/surefire-reports/*.xml'
            //     jacoco execPattern: 'target/jacoco.exec'
            //   }
            // }
        }    
      // Need to add SonarQube Checks 
      // stage('SonarQube - SAST') {
      //   steps {
      //     withSonarQubeEnv("SonarQube") {
      //       sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops-demo-eastus.cloudapp.azure.com:9000"
      //     }
      //     timeout(time: 2, unit: 'MINUTES') {
      //       script {
      //         waitForQualityGate abortPipeline: true
      //       }
      //     }
      //   }
      // }  
      // stage('Vulnerability Scan - Docker') {
      //  // NEED OWASP Dependency-Check Plugin installed
      //   steps {
      //     sh "mvn dependency-check:check"
      //    }
      // }
      stage('Vulnerability Scan - Docker') {
        steps {
          parallel (
              "Dependency Scan": {
                 sh "mvn dependency-check:check"
             },
               "Trivy Scan": {
                 sh "bash trivy-docker-image-scan.sh"
             },
             		"OPA Conftest":{
				          sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-docker-security.rego Dockerfile'
			       }   
          )
         }
      }
      stage('Docker Build & Push') {
            steps {
              withDockerRegistry([credentialsId: "docker-hub", url: '']) {
                sh "sudo docker build -t sjlevalley/devsecops-numeric-application:$GIT_COMMIT ."
                sh "docker push sjlevalley/devsecops-numeric-application:$GIT_COMMIT"
              }
            }
        }    
      stage('Kubernetes Deployment - DEV') {
            steps {
              withKubeConfig([credentialsId: "kubeconfig"]) {
                sh "sed -i 's#replace#sjlevalley/devsecops-numeric-application:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                sh "kubectl apply -f k8s_deployment_service.yaml"
              }
            }
        } 
    }
    post {
      always {
        junit 'target/surefire-reports/*.xml'
        jacoco execPattern: 'target/jacoco.exec'
        dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
      }
      // success {

      // }
      // failure {

      // }
    }
}