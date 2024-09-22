pipeline {
    agent none
    stages {
        stage('Compile') {
            agent any
            steps {
                sh 'mvn compile'
            }
        }
        stage('Code Quality') {
            agent any
            steps {
               sh 'mvn sonar:sonar -Dsonar.projectKey=sonarqube-swayam -Dsonar.host.url=http://localhost:9000 -X'

            }
        }
        stage('Test') {
            agent any
            steps {
                sh 'mvn test'
            }
        }
        stage('Package') {
            agent any
            steps {
                sh 'mvn package'
            }
        }
        stage('Upload War File To Artifactory') {
            agent any
            steps {
                sh 'echo Uploaded War file to Artifactory'
            }
        }
        stage('Deploy') {
            agent any
            steps {
                script {
                    sh '''
                    rm -rf dockerimg
                    mkdir dockerimg
                    cp /var/lib/jenkins/workspace/JenkinsfileJob/gameoflife-web/target/gameoflife.war dockerimg/
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
}
