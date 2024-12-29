pipeline {
    agent {
        label 'slave'
    }
    tools {
        maven 'maven'
    }
    environment {
        SONAR_HOST_URL = 'http://a631849c5f02d4dada69e672a5829841-796493282.us-east-2.elb.amazonaws.com:9000'  // Set your SonarQube URL here
        SONAR_AUTH_TOKEN = 'sqa_275cc93e76e3bc70a4090f006a54bfa80c49103f'  // Set your SonarQube authentication token here
        DOCKER_USR_NAME = 'arjundevops92'
        DOCKER_PWS = 'Arjun030692@'
        KUBECONFIG = '/root/.kube/config' 
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/srinivas895/springboot-k8s-yaml.git'
            }
        }
        stage('test'){
          steps {
            script {
                sh "mvn test"
            }
        }
       }
        stage('build'){
          steps {
            script {
                sh "mvn package"
            }
        }
       }
        stage('SonarQube Analysis') {
         steps {
          script {
             sh """
                mvn sonar:sonar \
                    -Dsonar.host.url=${env.SONAR_HOST_URL} \
                    -Dsonar.login=${env.SONAR_AUTH_TOKEN} \
                    -Dsonar.projectKey=${env.SONAR_PROJECT_KEY}
            """
            sh "ls -la target/"
            }
        }
    }
    stage('docker build'){
        steps {
            script {
                sh "docker build -t arjundevops92/test-repo:v3 ."
            }
        }
    }
    stage('Trivy image scan'){
        steps {
            script {
                sh "trivy image --format json --output trivy-report.json arjundevops92/test-repo:v3"
                archiveArtifacts artifacts: 'trivy-report.json'
            }
        }
    }
    stage ('Image push'){
        steps {
            script {
                sh "docker login -u ${DOCKER_USR_NAME} -p ${DOCKER_PWS}"
                sh "docker push arjundevops92/test-repo:v3"
            }
        }
    }
    stage('deploy application in eks cluster'){
        steps {
            script {
                sh "kubectl apply -f deployment.yaml"
            }
        }
    } 
  }
}
