register-app


##########pipeline to test jenkins token working##################

pipeline {
    agent{
        label "Jenkins-Agent"
    }
    
    environment {
            JENKINS_API_TOKEN2 = credentials("JENKINS_API_TOKEN")
            JENKINS_API_TOKEN = credentials("JNKN_TKN")
                                                 
            
    }

    stages {
       stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v --user clouduser:${JENKINS_API_TOKEN} 'ec2-13-126-219-252.ap-south-1.compute.amazonaws.com:8080/user/clouduser/api/json'"
                }
            }
       }
    }
}


####################################################################

