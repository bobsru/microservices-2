pipeline {
    agent any
    tools {
        jdk 'jdk8'
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
            }
            
        }

        stage ('Build') {
            steps {
                script{
                    def server = Artifactory.server('local-artifactory')
                    def rtMaven = Artifactory.newMavenBuild()
                    rtMaven.deployer.deployArtifacts = false
                    rtMaven.tool = 'maven-3.5.4'
                    def buildInfo = rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install'
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
                    rtMaven.deployer.deployArtifacts buildInfo
                    server.publishBuildInfo buildInfo

                }
            }
        }
    }
}
