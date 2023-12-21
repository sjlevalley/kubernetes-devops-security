@Library('slack') _

pipeline {
  agent any
  environment {
      deploymentName = 'devsecops'
      containerName = 'devsecops-container'
      serviceName = 'devsecops-svc'
      imageName = "sjlevalley/devsecops-numeric-application:${GIT_COMMIT}"
      applicationURL = 'http://devsecops-demo-1.eastus.cloudapp.azure.com'
      applicationURI = '/increment/99'
  }
  stages {
    //   stage('Build Artifact') {
    //   steps {
    //     sh 'mvn clean package -DskipTests=true'
    //     archive 'target/*.jar'
    //   }
    //   }
    //   stage('Unit Tests') {
    //   steps {
    //     sh 'mvn test'
    //   }
    //   }
    //   // Need to add SonarQube Checks
    //   // stage('SonarQube - SAST') {
    //   //   steps {
    //   //     withSonarQubeEnv("SonarQube") {
    //   //       sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops-demo-eastus.cloudapp.azure.com:9000"
    //   //     }
    //   //     timeout(time: 2, unit: 'MINUTES') {
    //   //       script {
    //   //         waitForQualityGate abortPipeline: true
    //   //       }
    //   //     }
    //   //   }
    //   // }
    //   stage('Vulnerability Scan - Docker') {
    //   steps {
    //     parallel(
    //         'Dependency Scan': {
    //             sh 'mvn dependency-check:check'
    //         },
    //           'Trivy Scan': {
    //             sh 'bash trivy-docker-image-scan.sh'
    //         },
    //           'OPA Conftest': {
    //             sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-docker-security.rego Dockerfile'
    //           }
    //     )
    //   }
    //   }
    //   stage('Docker Build & Push') {
    //   steps {
    //     withDockerRegistry([credentialsId: 'docker-hub', url: '']) {
    //       sh "sudo docker build -t sjlevalley/devsecops-numeric-application:$GIT_COMMIT ."
    //       sh "docker push sjlevalley/devsecops-numeric-application:$GIT_COMMIT"
    //     }
    //   }
    //   }
    //   stage('Vulnerability Scan - Kubernetes') {
    //     steps {
    //       parallel(
    //         'OPA Scan': {
    //           sh 'docker run --rm -v $(pwd):/project openpolicyagent/conftest test --policy opa-k8s-security.rego k8s_deployment_service.yaml'
    //         },
    //         'Kubesec Scan': {
    //           sh 'bash kubesec-scan.sh'
    //         },
    //         'Trivy Scan': {
    //           sh 'bash trivy-k8s-scan.sh'
    //         }
    //       )
    //     }
    //   }
    //   stage('K8S Deployment - DEV') {
    //   steps {
    //     parallel(
    //             'Deployment': {
    //               withKubeConfig([credentialsId: 'kubeconfig']) {
    //                 sh 'bash k8s-deployment.sh'
    //               }
    //             },
    //             'Rollout Status': {
    //               withKubeConfig([credentialsId: 'kubeconfig']) {
    //                 sh 'bash k8s-deployment-rollout-status.sh'
    //               }
    //             }
    //           )
    //   }
    //   }
    //   stage('Integration Tests - DEV') {
    //     steps {
    //       script {
    //         try {
    //           withKubeConfig([credentialsId: 'kubeconfig']) {
    //             sh 'bash integration-test.sh'
    //           }
    //         } catch (e) {
    //           withKubeConfig([credentialsId: 'kubeconfig']) {
    //             sh "kubectl -n default rollout undo deploy ${deploymentName}"
    //           }
    //           throw e
    //         }
    //       }
    //     }
    //   }
    // stage('OWASP ZAP - DAST') {
    //   steps {
    //     withKubeConfig([credentialsId: 'kubeconfig']) {
    //       sh 'bash zap.sh'
    //     }
    //   }
    // }
      stage('Testing Slack') {
      steps {
        sh 'exit 0'
      }
  }
  post {
    always {
      // junit 'target/surefire-reports/*.xml'
      // jacoco execPattern: 'target/jacoco.exec'
      // dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
      // publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'owasp-zap-report', reportFiles: 'zap_report.html', reportName: 'OWASP ZAP HTML Report', reportTitles: 'OWASP ZAP HTML Report', useWrapperFileDirectly: true])
      sendNotifications.currentBuild.result
    }
  }
}
