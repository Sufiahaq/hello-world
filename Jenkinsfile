pipeline{
    agent any
    tools{
        jdk 'jdk11'
        maven 'maven'
    } 
    
     
     environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
      

    stages {
        stage('Code Checkout') {
            steps {
             git changelog: false, poll: false, url: 'https://github.com/Sufiahaq/hello-world.git'
            }
        }
  stage('Maven Build'){
            steps {
                sh 'mvn clean package -DskipTests'
            }
            
        }
        
        stage('JUnit Test') {
          steps {
            script {
              sh 'mvn test'
              junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'
            }
          }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=sonarcicd \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=sonarcicd '''
    
                }
            }
        }
        stage("jfrog") {
            steps{
                rtServer (
    id: 'Artifactory-1',
    url: 'http://13.239.34.239:8082/artifactory/',
        // If you're using username and password:
    username: 'admin',
    password: 'Telus@123',
        // If you're using Credentials ID:
    //    credentialsId: 'jfrog-token',
        // If Jenkins is configured to use an http proxy, you can bypass the proxy when using this Artifactory server:
        // bypassProxy: true,
        // Configure the connection timeout (in seconds).
        // The default value (if not configured) is 300 seconds: 
        timeout: 300
)
rtUpload (
    serverId: 'Artifactory-1',
    spec: '''{
          "files": [
            {
              "pattern": "webapp/target/**.war",
              "target": "myartifact/com/example/maven-project/webapp"
            }
         ]
    }''',

)
            }
        } 
        stage("docker image") {
            steps {
                script {
                    sh "docker build -t sufiahaq/test:${BUILD_ID} ./"
                }
            }
        }
        stage("push image to dockerhub") {
        steps {
            sh "docker push sufiahaq/test:${BUILD_ID}"
        }
        }

 
        
        
    }
}

