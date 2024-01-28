pipeline{
    agent{
        label "Jenkins-Agent"
    }
    tools {
        jdk "Java17"
        maven "Maven3"
    }
    stages{
        stage("Cleanup WS"){
            steps{
                cleanWs()
            }
          
        }

        stage("chkout SCM"){
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/pranaygitac/devopscicd.git'
            }
        }

        stage("Build app"){
            steps {
                sh "mvn clean package"
            }    
        } 

        stage("Test app"){
            steps {
                sh "mvn test"
            }    
        } 
    }
    
}