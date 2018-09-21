pipeline {
    agent any
    tools {
        jdk 'jdk8'
    }
    environment{
        server = null
        rtMaven = null
        buildInfo = null
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
                script{
                    
                    rtMaven = Artifactory.newMavenBuild()
                    rtMaven.deployer.deployArtifacts = false
                    rtMaven.tool = 'maven-3.5.4'
                    
                }
            }
            
        }

        stage ('Build') {
            steps {
                script{
                    
                    buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
                }
            }
            post {
                success {
                    junit 'target/surefire-reports/**/*.xml' 
                }
            }
        }
        stage('Publish'){
            steps{
                script{
                    server = Artifactory.newServer url: 'http://localhost:8081/artifactory', username: 'admin', password: 'admin'
                    rtMaven.deployer.deployArtifacts buildInfo
                    server.publishBuildInfo buildInfo

                }
            }
        }
    }
}
