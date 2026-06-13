pipeline {
  agent any
  tools {
    Maven 'Maven-3.9.16'  // name doesn't matter, Jenkins will use system maven
  }
  environment {
    NEXUS_URL = 'http://nexus:8081'
    SONAR_URL = 'http://sonarqube:9000'
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build + Test') {
      steps {
        sh 'mvn clean verify'
      }
    }
    stage('Deploy to Nexus') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'nexus-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'mvn deploy -DskipTests -DaltDeploymentRepository=snapshots::default::http://${USER}:${PASS}@nexus:8081/repository/maven-snapshots/'
        }
      }
    }
    stage('SonarQube Scan') {
      steps {
        withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
          sh 'mvn sonar:sonar -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=$SONAR_TOKEN'
        }
      }
    }
  }
}
