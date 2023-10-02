def call(body) {
def config = [:]
body.resolveStrategy = Closure.DELEGATE_FIRST
body.delegate = config
body()
}


pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
     environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Gaetanneo/Petclinic-app.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
      
        stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        
          stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ ' , odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'Docker-credentialsId', url: 'https://registry.hub.docker.com/') {
                        
                        sh "docker build -t petclinic1 ."
                        sh "docker tag petclinic1 gaetanneo/pet-clinic1:latest"
                        echo "ALice@@2018" | docker login --username gaetanneo --password-stdin 'https://registry.hub.docker.com/'

                        sh "docker push gaetanneo/pet-clinic1:latest"
                    
                  }
               }
           }
      }
  }
}
