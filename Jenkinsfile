def rtServer, buildInfo, rtNpm

pipeline {
    agent { label 'master' }
    tools {
      nodejs 'NJS11'
    }
    parameters {
        string (name: 'ART_URL', defaultValue: 'http://localhost:8081/artifactory', description: 'Artifactory where artifacts will be deployed/resolved')
        string (name: 'ART_USER', defaultValue: 'admin', description: 'Artifactory user for deploy/resolve artifacts')
        string (name: 'ART_PASSWORD', defaultValue: 'password', description: 'Artifactory password for deploy/resolve artifacts')
    //    string (name: 'ART_REPO', defaultValue: 'npm', description: 'Virtual Repository where artifacts will be deployed/resolved (Releases)')
    }
    
    stages {
        stage('Init properties'){
            steps {
                script {
                    rtServer = Artifactory.newServer url: "${params.ART_URL}", username: "${params.ART_USER}", password: "${params.ART_PASSWORD}"
                    rtNpm = Artifactory.newNpmBuild()
                    buildInfo = Artifactory.newBuildInfo()
                    // This variables should be PARAMETERIZED
                    rtNpm.deployer repo: 'npm-local', server: rtServer
                    rtNpm.resolver repo: 'npm-remote', server: rtServer
                    rtNpm.deployer.deployArtifacts = false
                    //rtNpm.executablePath = (String) "$NODEJS_HOME/bin/npm"
                }
            }
        }
        
        stage ('Checkout') {
            steps {
                git url: 'https://github.com/sanyam-batra/Angular_Jenkins.git'
                }
        }
        
        stage ('Npm Install') {
            steps {
                script{
                    rtNpm.install buildInfo: buildInfo, path: 'https://github.com/sanyam-batra/Angular_Jenkins'
                }
            }
        }
    
        stage ('Publish npm') {
            steps {
                script {
                    rtNpm.publish buildInfo: buildInfo, path: 'https://github.com/sanyam-batra/Angular_Jenkins'
                }
            }
        }
    
        stage ('Publish build info') {
            steps {
                script {
                    rtServer.publishBuildInfo buildInfo
                }
            }
        }
        
    }
}
