pipeline {
    agent any
    stages{
        stage ('code checkout'){
         steps{
             git url:'https://github.com/BALACHANDRAVK/cicd-project.git', branch: "master"
            }   
        }
       stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonar-pwd', installationName: 'SonarQube') {
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=cicd"
                }
            }
        }
        stage('Moven Build'){
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('upload to arti') {
            steps {
                dir('/var/lib/jenkins/workspace/cicd/target') {
                    nexusArtifactUploader artifacts: [[artifactId: 'cicd-artifact-id', classifier: '', file: 'devops-integration.jar', type: 'jar']], credentialsId: 'nexus-arti', groupId: 'com.truelearning', nexusUrl: '43.204.102.220:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'nexus-test', version: '0.0.1-SNAPSHOT'
                }
            }
        }
                    
        stage('build Docker image'){
            steps{
                script{
                    sh 'docker build -t balachandravk/cicd:v11.01 .'
                }
            }
        }
        stage('Docker login'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker-pwd', passwordVariable: 'PASS', usernameVariable: 'USER')]){
                     sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push balachandravk/cicd:v11.01'
                }
                
            }
        }
        
        stage('Deploy to k8s'){
            when{ expression {env.GIT_BRANCH == 'origin/master'}}
            steps{
                script{
                     kubernetesDeploy (configs: 'deploysvs.yaml' ,kubeconfigId: 'k8spwd-ssh')
                   
                }
            }
        }
        
    }
}
