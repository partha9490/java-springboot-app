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
        stage("build code"){
            steps {
                echo "----------- build started ----------"
                sh 'mvn clean package -Dmaven.test.skip=true'
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
                                "target": "partha-new/{1}",
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
        stage(" Create Docker Image ") {
            steps {
                script {
                    echo '-------------- Docker Build Started -------------'
                    app = docker.build("meportal12.jfrog.io/partha-new1/myapp:1.0")
                    echo '-------------- Docker Build Ended -------------'
                }
            }
        }
        stage (" Docker Publish "){
            steps {
                script {
                        echo '---------- Docker Publish Started --------'  
                        docker.withRegistry("https://meportal12.jfrog.io", 'jfrog-cred'){
                        app.push()
                        echo '------------ Docker Publish Ended ---------'  
                    } 
                }
            }
        }        

    }
}
