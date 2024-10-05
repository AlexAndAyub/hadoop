pipeline {
    agent any

    environment {
        HADOOP_VER = 3.2.2
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
                       -v "${PWD}/hadoop-${HADOOP_VER}/:/home/jenkins/hadoop${V_OPTS:-}" \
                       -w "/home/jenkins/hadoop" \
                       -v "${HOME}/.m2:/home/genkins/.m2${V_OPTS:-}" \
                       -u "1001" \
                       "hadoop-build-1001" mvn package -Pdist -DskipTests -Dtar -Dmaven.javadoc.skip=true
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
