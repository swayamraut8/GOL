pipeline {
    agent none

    stages {
        stage('Compile') {
            agent any
            steps {
                echo 'Compiling the project...'
                sh 'mvn compile'
            }
        }

        stage('Code Quality') {
            agent any
            steps {
                echo 'Running SonarQube analysis...'
                sh 'mvn sonar:sonar -Dsonar.projectKey=sonarqube-swayam -Dsonar.host.url=http://localhost:9000'
            }
        }

        stage('Test') {
            agent any
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }

        stage('Package') {
            agent any
            steps {
                echo 'Packaging the application...'
                sh 'mvn package'
            }
        }

        stage('Upload War File To Artifactory') {
            agent any
            steps {
                echo 'Uploading War file to Artifactory...'
                sh 'echo Uploaded War file to Artifactory'
                // Add actual upload command here if needed
            }
        }

        stage('Deploy') {
            agent any
            steps {
                script {
                    echo 'Deploying the application...'
                    sh '''
                    rm -rf dockerimg
                    mkdir dockerimg
                    cp target/gameoflife.war dockerimg/
                    cd dockerimg
                    echo "FROM tomcat" > Dockerfile
                    echo "ADD gameoflife.war /usr/local/tomcat/webapps/" >> Dockerfile
                    echo 'CMD ["catalina.sh", "run"]' >> Dockerfile
                    echo "EXPOSE 8080" >> Dockerfile
                    sudo docker build -t webimage:$BUILD_NUMBER .
                    sudo docker run -itd --name webserver$BUILD_NUMBER -p 8080:8080 webimage:$BUILD_NUMBER
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
        always {
            echo 'Cleaning up...'
            // Optional cleanup steps
        }
    }
}
