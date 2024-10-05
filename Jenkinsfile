pipeline {
    agent any

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-1.8.0-openjdk-amd64'
        MAVEN_HOME = '/opt/apache-maven-3.9.9'
        PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'branch-3.2.2', url: 'https://github.com/apache/hadoop.git'
            }
        }

        stage('Check JDK Version') {
            steps {
                sh '''
                    java -version
                '''
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    docker run --rm=true $DOCKER_INTERACTIVE_RUN \
                       -v "${PWD}:/home/jenkins/hadoop${V_OPTS:-}" \
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
