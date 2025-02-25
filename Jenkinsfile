pipeline { 
    agent any 
    environment { 
        SONAR_SCANNER_HOME = tool 'sonar7.0'  // Matches the SonarScanner tool name in Jenkins 
        SONAR_HOST_URL = 'http://65.2.73.67:9000/'  // Replace with your SonarQube server address 
        SONAR_AUTH_TOKEN = credentials('sonartoken')  // Fetch SonarQube authentication token from Jenkins credentials
    } 
    stages { 
        stage('Clone Repository') { 
            steps { 
                echo 'Cloning the repository...' 
                git branch: 'main', url: 'https://github.com/Nithish1641/mavenproject.git' 
            } 
        } 
        stage('Build') { 
            steps { 
                echo 'Building the project...' 
                sh 'mvn clean install' 
            } 
        } 
        stage('Test') { 
            steps { 
                echo 'Running tests...' 
                sh 'mvn test' 
            } 
        } 
        stage('Archive Artifacts') { 
            steps { 
                echo 'Archiving build artifacts...' 
                archiveArtifacts artifacts: '**/target/*.jar', onlyIfSuccessful: true 
            } 
        } 
        stage('Checkstyle Analysis') { 
            steps { 
                echo 'Running Checkstyle analysis...' 
                sh 'mvn checkstyle:checkstyle' 
            } 
        } 
        stage('SonarQube Analysis') { 
            steps { 
                echo 'Running SonarQube analysis...' 
                withSonarQubeEnv('sonar7.0') {  // Ensure 'sonar7.0' matches the SonarQube configuration in Jenkins 
                    withCredentials([string(credentialsId: 'sonartoken', variable: 'SONAR_AUTH_TOKEN')]) { 
                        sh ''' 
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner  
                            -Dsonar.projectKey=MavenProject \ 
                            -Dsonar.projectName=MavenProject \ 
                            -Dsonar.projectVersion=1.0 \ 
                            -Dsonar.host.url=${SONAR_HOST_URL} \ 
                            -Dsonar.login=${SONAR_AUTH_TOKEN} \ 
                            -Dsonar.sources=src \ 
                            -Dsonar.java.binaries=target/classes \ 
                            -Dsonar.junit.reportsPath=target/surefire-reports \ 
                            -Dsonar.jacoco.reportsPath=target/jacoco.exec \ 
                            -Dsonar.java.checkstyle.reportPaths=target/site/checkstyle-result.xml 
                        ''' 
                    } 
                } 
            } 
        } 
    } 
    post { 
        success { 
            echo 'Pipeline completed successfully!' 
        } 
        failure { 
            echo 'Pipeline failed!' 
        } 
        always { 
            echo 'Pipeline execution completed.' 
        } 
    } 
}
