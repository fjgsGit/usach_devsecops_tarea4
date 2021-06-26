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
        stage ('SCA') {
            steps {
                echo '========================================='
                echo '                SCA OWAS - Dependecy Check '
                echo '========================================='
                 sh 'mvn dependency-check:check'  
                 dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'  
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
        stage('DAST'){
            steps{
                script{
                    env.DOCKER_EXEC = "docker"
                    env.TARGET = 'https://zero.webappsecurity.com/'
                    sh '${DOCKER_EXEC} rm -f zap2'
                    sh '${DOCKER_EXEC} pull owasp/zap2docker-stable'
                    sh '${DOCKER_EXEC} run --add-host="localhost:192.168.3.4" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8090:8080 -d owasp/zap2docker-stable zap.sh -daemon -port 8080 -host 0.0.0.0 -config api.disablekey=true'
                    sh '${DOCKER_EXEC} run --add-host="localhost:192.168.3.4" -v /var/jenkins_home/tools:/zap/wrk/:rw --rm -i owasp/zap2docker-stable zap-baseline.py -t ${TARGET} -I -r zap_report.html -l PASS'
                }
            }
        }
        stage('Publish DAST'){
			steps{
				publishHTML([
				    allowMissing: false,
				    alwaysLinkToLastBuild: false,
				    keepAll: false,
				    reportDir: '/var/jenkins_home/tools',
				    reportFiles: 'zap_report.html',
				    reportName: 'HTML Report',
				    reportTitles: ''])				    
			}
        }
        stage('Scan Docker'){
            steps{
                script{
                    //env.DOCKER = tool "Docker"
                    env.DOCKER_EXEC = "docker"
                    env.TARGET_IMG = "usach/pheasoy:1.0"
                    sh '${DOCKER_EXEC} run --rm -v /var/run/docker.sock:/var/run/docker.sock -v $HOME/Library/Caches:/root/.cache/ aquasec/trivy:0.18.3 ${TARGET_IMG}'
                    sh '${DOCKER_EXEC} rmi aquasec/trivy:0.18.3'
                }
            }
        }
    } 
}

