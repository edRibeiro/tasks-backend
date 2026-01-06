pipeline {
  agent any
  stages {
    stage('Build Backend') {
      steps {
        bat 'mvn clean package -DskipTests=true'
      }
    }
    stage('Unit Tests') {
      steps {
        bat 'mvn test'
      }
    }
    stage('SonarQube Analysis') {
      environment {
        SONARQUBE_SCANNER_HOME = tool 'SONAR_SCANNER'
      }
      steps {
        withSonarQubeEnv('SONAR_LOCAL') {
          bat "${SONARQUBE_SCANNER_HOME}/bin/sonar-scanner.bat -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=d9d8a9a17009494d6a1a338897527ff783bd93ca -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
        }
      }
    }
    stage('Quality Gate') {
      steps {
        sleep(5)
        timeout(time: 1, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
    stage('Deploy Backend') {
      steps {
        deploy adapters: [tomcat8(alternativeDeploymentContext: '', credentialsId: 'TomcatLogin', path: '', url: 'http://127.0.0.1:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
      }
    }
  }
}
