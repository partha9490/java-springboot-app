pipeline {
       agent {
        node {
            label 'jenkins-slave-node'
        }
    }

    environment {
        PATH = "/opt/apache-maven-3.9.6/bin:$PATH"
    }

    stages {
        stage("Build Stage"){
            steps {
                echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- build completed ----------"
            }
        }
         
        stage("Test Stage"){
            steps{
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                echo "----------- unit test Completed ----------"
            }
        }
        /*
        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonar-scanner-meportal'
            }
            steps{
                withSonarQubeEnv('sonar-server-meportal') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage("Quality Gate"){
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') { 
                        def qg = waitForQualityGate() 
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
        */
        stage("Artifact Publish") {
            steps {
                script {
                    echo '------------- Artifact Publish Started ------------'
                    def server = Artifactory.newServer url:"https://meportal12.jfrog.io/artifactory" ,  credentialsId:"jfrog-cred"
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "staging/(*)",
                                "target": "jfrog-cred/{1}",
                                "flat": "false",
                                "props" : "${properties}",
                                "exclusions": [ "*.sha1", "*.md5"]
                            }
                        ]
                    }"""
                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)
                    echo '------------ Artifact Publish Ended -----------'  
                }
            }   
        }

    }
}
