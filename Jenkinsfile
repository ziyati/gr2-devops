pipeline {
  agent any
  tools {
    maven 'maven'
  }
 environment {
    SONAR_URL = "http://localhost:9000"
  }
  
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', credentialsId: 'git-connecting', url: 'https://github.com/ziyati/gr2-devops'
      }
    }

    stage('Build') {
      steps {
        sh 'mvn clean package'
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t java_code:01 .'
      }
    }

    stage('Docker Tag') {
      steps {
        sh 'docker tag java_code:01 127.0.0.1:8082/myrep/java_code:01'
      }
    }

    stage('Connect Nexus') {
      steps {
        sh 'echo "Passw.rd1234" | docker login -u admin --password-stdin 127.0.0.1:8082/repository/myrep/'
      }
    }

    stage('Push Image') {
      steps {
        sh 'docker push 127.0.0.1:8082/myrep/java_code:01'
      }
    }
    
    stage("Prepare") {
      steps {
        sh 'mvn clean org.jacoco:jacoco-maven-plugin:prepare-agent install'
      }
    }
    stage("SonarQube Analysis") {
      steps {
        sh 'mvn sonar:sonar -Dsonar.host.url=$SONAR_URL'
      }
    }
    stage("Kubernetes") {
      steps {
        sh 'kubectl create -f deployment.yaml'
      }
    }
    
    
  }
}