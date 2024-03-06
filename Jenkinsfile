pipeline {
    agent any
    stages{
        stage('Build Maven'){
            steps{
                git url:'https://github.com/BALACHANDRAVK/cicd-project/', branch: "master"
               sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t balachandravk/cicd:v1.0 .'
                }
            }
        }
          stage('Docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push balachandravk/cicd:v1.0'
                }
            }
        }
        
        
        stage('Deploy to k8s'){
            when{ expression {env.GIT_BRANCH == 'origin/master'}}
            steps{
                script{
                     kubernetesDeploy (configs: 'deploymentservice.yaml' ,kubeconfigId: 'k8sconfigpwd')
                   
                }
            }
        }
    }
}
