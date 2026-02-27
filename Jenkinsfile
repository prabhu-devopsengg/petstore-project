pipeline{
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
     environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
     stages{
        stage ('clean Workspace'){
            steps{
                cleanWs()
            }
        }
        stage ('checkout scm') {
            steps {
                git 'https://github.com/prabhu-devopsengg/petstore-project.git'
            }
        }
         stage ('maven compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
         stage ('maven Test') {
            steps {
                sh 'mvn test'
            }
        }
         stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=petstore \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=petstore '''
                }
            }
        }
         stage("quality gate"){
            steps {
                script {
                  waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
           }
        }
        stage ('Build war file'){
            steps{
                sh 'mvn clean install -DskipTests=true'
            }
        }
       stage('Install Docker & Deploy') {
            steps {
                dir('Ansible'){
                  script {
                         ansiblePlaybook credentialsId: 'ssh', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/', playbook: 'docker.yaml'
                        }
                     }
                }
           }      
      }
 }

