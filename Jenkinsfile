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
                      -v "${PWD}:/home/jenkins/hadoop" \
                      -w "/home/jenkins/hadoop" \
                      -v "${HOME}/.m2:/home/jenkins/.m2" \
                      -u "${USER_ID}" \
                      "hadoop-build-1001" \
                      mvn package -Pdist -DskipTests -Dtar -Dmaven.javadoc.skip=true
                '''
            }
        }

    }

    post {
        always {
            archiveArtifacts artifacts: 'hadoop-dist/target/hadoop-*.gz', allowEmptyArchive: true
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
