pipeline {
    agent any

    tools {
        maven 'Maven'      
    }

    stages {
      stage ('Setup') {
            steps {
              echo '========================================='
              echo '                Setup  '
              echo '========================================='
              sh '''
                   echo "PATH = ${PATH}"
                   echo "M2_HOME = ${M2_HOME}"
               '''
            }
        }
        stage ('Compile') {
            steps {
                echo '========================================='
                echo '                Build '
                echo '========================================='
                 sh 'mvn clean compile -e'
            }
        }
        stage ('Test') {
            steps {
                echo '========================================='
                echo '                Test '
                echo '========================================='
                 sh 'mvn clean test -e'
            }
        }

        stage('SAST') {
           steps{
               echo '========================================='
              echo '                SAST - SONARQUBE '
              echo '========================================='
                                
                      sh 'mvn sonar:sonar -Dsonar.projectKey=USACH_DEVSECOPS_PROJECT -Dsonar.host.url=http://localhost:9000 -Dsonar.login=60b6e5a53c50e2b48ee19e0f3c7643fc86ef3a2b'
            
            }
        }
    } 
}

