pipeline {
    agent {
        node {
            label 'jenkins-slave-node'
        }
    }
    stages {
        stage "build code" {
            steps {
                echo "----------- build started ----------"
                sh 'mvn clean package -Dmaven.test.skip=true'
                echo "----------- build completed ----------"
  
            }
        }
    }
}