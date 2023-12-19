pipeline {
  agent any
  environment {
      deploymentName = "devsecops"
      containerName = "devsecops-container"
      serviceName = "devsecops-svc"
      imageName = "sjlevalley/devsecops-numeric-application:${GIT_COMMIT}"
      applicationURL="devsecops-demo-1.eastus.cloudapp.azure.com"
      applicationURI="/increment/99"
    }
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
      stage('Vulnerability Scan - Kubernetes') {
            steps {
              parallel(
                "OPA Scan": {
                  sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-k8s-security.rego k8s_deployment_service.yaml'
                }
              )
            }
    }
      stage('K8S Deployment - DEV') { 
            steps {
              parallel(
                "Deployment": {
                  withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh "bash k8s-deployment.sh"
                  }
                },
                "Rollout Status": {
                  withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh "bash k8s-deployment-rollout-status.sh"
                  }
                }
              )
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
}