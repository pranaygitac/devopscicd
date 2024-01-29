pipeline{
    agent{
        label "Jenkins-Agent"
    }
    tools {
        jdk "Java17"
        maven "Maven3"
    }

     environment {
	        APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "psawant05"
            DOCKER_PASS = 'dockerhub'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
            JENKINS_API_TOKEN2 = credentials("JENKINS_API_TOKEN")
            JENKINS_API_TOKEN = credentials("JNKN_TKN")
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

         stage("SQ analysis"){
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jk-sq-token') {
                    sh "mvn sonar:sonar"
                    } 
                }
            }    
        }

        stage("Quality Gate"){
            steps {
               script {
                waitForQualityGate abortPipeline: false, credentialsId: 'jk-sq-token'
               }
            }    
        }

         stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }

       }

        stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image psawant05/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }

       stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
       }  

        stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh '''curl -v -k --user clouduser:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-13-126-219-252.ap-south-1.compute.amazonaws.com:8080/job/cdjob/buildWithParameters?token=gitops-token'
			'''
                }
            }
       }
    }
    
}
