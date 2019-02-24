pipeline {
    agent any

    options {
        retry(3)
    }

    tools {
        maven "Maven 3.3.9"
        jdk "jdk8"
    }

    stages {
        stage('Checkout environment') {
            steps {
                echo "Hello, Maven & Java"
                sh "mvn --version"
                sh "java -version"
            }
        }

        stage("Build the database and create the sample data") {
            steps {
                echo "Building database..."
                sh "mvn -f pom-data.xml process-test-classes"
            }
        }
        stage('Maven Test') {
            steps{
                echo "Maven testing..."
		sh "mvn clean test verify checkstyle:checkstyle"
            }
        }   
    }
}
