pipeline {

     environment { 
        registry = "nagireddylegend05/my-app" 
        registryCredential = 'DockerCred' 
        dockerImage = '' 
    }
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                //git 'https://github.com/Nagireddylegend/my-app.git'
                 git branch: 'main', credentialsId: 'Nagicredentials', url: 'https://github.com/Nagireddylegend/my-app.git'
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
      stage('Building our image') { 
            steps { 
                script { 
                    dockerImage = docker.build registry + ":$BUILD_NUMBER" 
                }
            } 
        }
        stage('Deploy our image') { 
            steps { 
                script { 
                  docker.withRegistry( '', registryCredential ) { 
                        dockerImage.push() 
                    }
                } 
            }
        } 
         stage("SSH Into k8s Server") {
        def remote = [:]
        remote.name = 'K8S master'
        remote.host = 'host IP'
        remote.user = 'sever username'
        remote.password = 'server Password'
        remote.allowAnyHosts = true

        stage('Put my-app-deployment-into-k8s-master') {
            sshPut remote: remote, from: 'my-app-deployment.yml', into: '.'
        }
              stage('Deploy spring boot') {
          sshCommand remote: remote, command: "kubectl apply -f my-app-deployment.yml"
        }
    }
