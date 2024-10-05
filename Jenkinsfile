pipeline {
    agent any

    environment {
        HADOOP_VER = "3.2.2"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "branch-${HADOOP_VER}", url: 'https://github.com/apache/hadoop.git'
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    docker run --rm=true $DOCKER_INTERACTIVE_RUN \
                      -v "${PWD}:/home/${USER_NAME}/hadoop" \
                      -w "/home/${USER_NAME}/hadoop" \
                      -v "${HOME}/.m2:/home/${USER_NAME}/.m2" \
                      -u "${USER_ID}" \
                      "hadoop-build-${USER_ID}" \
                      mvn package -Pdist -DskipTests -Dtar -Dmaven.javadoc.skip=true
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    mvn test
                '''
            }
        }

        stage('Package') {
            steps {
                sh '''
                    mvn package
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            junit '**/target/surefire-reports/*.xml'
        }

        success {
            echo 'Build succeeded!'
        }

        failure {
            echo 'Build failed!'
        }
    }
}
