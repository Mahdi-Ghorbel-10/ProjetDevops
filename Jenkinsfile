pipeline {
    agent any
    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }
    environment {
        DOCKER_COMPOSE_PATH = '/usr/local/bin/docker-compose'
        DEPENDENCY_CHECK_CACHE_DIR = '/var/jenkins_home/.m2/repository/org/owasp/dependency-check'
        MY_SECRET_KEY = 'dummy_value_for_testing'
        // TWILIO_ACCOUNT_SID = credentials('TWILIO_ACCOUNT_SID')
        // TWILIO_AUTH_TOKEN = credentials('TWILIO_AUTH_TOKEN')
        HOST_IP = '192.168.1.224' // IP of the host machine
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Mahdi-Ghorbel-10/ProjetDevops/'
            }
        }
        
        stage('Compile Project') {
            steps {
                sh 'mvn clean compile'
            }
            post {
                failure {
                    echo 'Compilation failed!'
                }
            }
        }
        
        stage('Run Unit Tests') {
            steps {
                sh 'mvn test -U'
            }
            post {
                failure {
                    echo 'Tests failed!'
                }
            }
        }
        
        stage('Generate JaCoCo Coverage Report') {
            steps {
                sh 'mvn jacoco:report'
            }
        }
        
        stage('Publish JaCoCo Report') {
            steps {
                step([$class: 'JacocoPublisher',
                      execPattern: '**/target/jacoco.exec',
                      classPattern: '**/classes',
                      sourcePattern: '**/src',
                      exclusionPattern: '*/target/**/,**/*Test*,**/*_javassist/**'
                ])
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sq1') {
                    sh 'mvn sonar:sonar'
                }
            }
            post {
                failure {
                    echo 'SonarQube scan failed!'
                }
            }
        }
        
        stage('Security Vulnerability Scan - Dependency Check') {
            steps {
                sh '''
                    mvn verify -Ddependency-check.skip=false \
                    -Ddependency-check.failBuildOnCVSS=7 \
                    -Ddependency-check.threads=1 \
                    -Ddependency-check.autoUpdate=false \
                    dependency-check:aggregate
                '''
            }
            post {
                failure {
                    echo 'Dependency-Check failed! Found vulnerabilities in dependencies.'
                }
                success {
                    echo 'No vulnerabilities found in dependencies.'
                }
            }
        }
        
        stage('Publish OWASP Dependency-Check Report') {
            steps {
                step([$class: 'DependencyCheckPublisher',
                      pattern: '**/dependency-check-report.html',
                      healthy: '0',
                      unhealthy: '1',
                      failureThreshold: '1'
                ])
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ghorbelmahdi/alpine .'
            }
            post {
                failure {
                    echo 'Docker image build failed!'
                }
            }
        }
        
        stage('Push Docker Image to Hub') {
            steps {
                script {
                    echo "Attempting Docker login with user: ghorbelmahdi"
                    withCredentials([usernamePassword(credentialsId: 'Docker', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_TOKEN')]) {
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_TOKEN}"
                        sh 'docker push ghorbelmahdi/alpine'
                    }
                }
            }
            post {
                success {
                    echo 'Docker image pushed successfully!'
                }
                failure {
                    echo 'Docker image push failed!'
                }
            }
        }
        
        stage('Deploy to Nexus Repository') {
            steps {
                sh '''
                    mvn deploy -DskipTests \
                    -DaltDeploymentRepository=deploymentRepo::default::http://${HOST_IP}:8081/repository/maven-releases/
                '''
            }
            post {
                success {
                    echo 'Deployment to Nexus was successful!'
                }
                failure {
                    echo 'Deployment to Nexus failed!'
                }
            }
        }
        
        stage('Deploy with Docker Compose') {
            steps {
                sh "docker compose -f docker-compose.yml -H tcp://${HOST_IP}:2375 up -d"
            }
            post {
                failure {
                    echo 'Docker compose up failed!'
                }
            }
        }
        
        stage('Start Monitoring Containers') {
            steps {
                sh '''
                    docker start \
                    5-nids-1-rayen-balghouthi-g1-prometheus-1 \
                    grafana -H tcp://${HOST_IP}:2375
                '''
            }
            post {
                failure {
                    echo 'Failed to start monitoring containers!'
                }
            }
        }
        
        stage('Make Script Executable') {
            steps {
                sh 'chmod +x ./run_security_smoke_tests.sh'
            }
        }
        
        stage('Security Smoke Tests') {
            steps {
                sh './run_security_smoke_tests.sh'
            }
            post {
                failure {
                    echo 'Security smoke tests failed!'
                }
            }
        }
        
        stage('Server Hardening Validation - Lynis') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh '''
                        sudo mkdir -p /tmp/lynis_reports
                        sudo lynis audit system --quick --report-file /tmp/lynis_reports/lynis-report.dat
                        sudo cp /tmp/lynis_reports/lynis-report.dat /tmp/lynis_reports/lynis-report.html
                        sudo chmod -R 777 /tmp/lynis_reports/
                        sudo sed -i '1s/^/<html><body><pre>/' /tmp/lynis_reports/lynis-report.html
                        echo "</pre></body></html>" >> /tmp/lynis_reports/lynis-report.html
                    '''
                }
            }
        }
        
        stage('Publish Lynis Report') {
    steps {
        publishHTML([
            reportName: 'Lynis Report',
            reportDir: '/tmp/lynis_reports',
            reportFiles: 'lynis-report.html',
            alwaysLinkToLastBuild: true,
            allowMissing: false,
            keepAll: true
        ])
    }
}

        
        stage('Send SMS Notification') {
            steps {
                script {
                    def message = """
                    Build Status: ${currentBuild.currentResult}
                    Build Number: ${currentBuild.number}
                    Project: ${env.JOB_NAME}
                    Duration: ${currentBuild.durationString}
                    """
                    sh """
                    curl 'https://api.twilio.com/2010-04-01/Accounts/${TWILIO_ACCOUNT_SID}/Messages.json' -X POST \
                        --data-urlencode 'To=+21628221389' \
                        --data-urlencode 'MessagingServiceSid=MG6f26b98c01c74e1ecef4eacb9ccd7b3e' \
                        --data-urlencode 'Body=${message}' \
                        -u ${TWILIO_ACCOUNT_SID}:${TWILIO_AUTH_TOKEN}
                    """
                }
            }
        }
        
        stage('Send Email Notification') {
            steps {
                script {
                    def subject = currentBuild.currentResult == 'SUCCESS' ? 
                        "✅ Build Success: ${currentBuild.fullDisplayName}" : 
                        "❌ Build Failure: ${currentBuild.fullDisplayName}"
                    
                    def body = """
                    Build Status: ${currentBuild.currentResult}
                    Build Number: ${currentBuild.number}
                    Project: ${env.JOB_NAME}
                    Duration: ${currentBuild.durationString}
                    Build URL: ${env.BUILD_URL}
                    """
                    
                    emailext subject: subject,
                             body: body,
                             mimeType: 'text/plain',
                             to: 'rayenbal55@gmail.com'
                }
            }
        }
    }
}
