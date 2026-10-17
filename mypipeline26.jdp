pipeline {
    agent any

    /*
     * This path assumes a Debian/Ubuntu Jenkins agent with OpenJDK 21 installed.
     * Current Jenkins LTS releases beginning with 2.555.1 require Java 21 or 25
     * for the controller and agents.
     */
    environment {
        JAVA_HOME = '/usr/lib/jvm/java-21-openjdk-amd64'
        PATH+JAVA21 = '/usr/lib/jvm/java-21-openjdk-amd64/bin'
    }

    stages {
        stage('PULL STAGE') {
            steps {
                git 'https://github.com/chetanraval07/batch2728-project.git'
                echo 'PULL SUCCESS'
            }
        }

        stage('VERIFY JAVA 21') {
            steps {
                sh 'java -version'
                sh './backend/mvnw -version'
            }
        }

        stage('BUILD AND TEST') {
            steps {
                dir('backend') {
                    sh './mvnw -B clean verify'
                }
            }
        }

        stage('SONARQUBE ANALYSIS') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    dir('backend') {
                        sh './mvnw -B sonar:sonar -Dsonar.projectKey=my-appB2728 -Dsonar.projectName=my-appB2728'
                    }
                }
            }
        }

        stage('QUALITY GATE') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true, credentialsId: 'sonar-cred'
                }
                echo 'QUALITY-PASSED'
            }
        }

        stage('ARCHIVE ARTIFACT TO S3') {
            steps {
                sh 'aws s3 cp backend/target/student-registration-backend-0.0.1-SNAPSHOT.jar s3://for-jenkins-project-backup-buxx/student-app-B2827.jar'
            }
        }

        stage('DEPLOY STAGE') {
            steps {
                echo 'Artifact archived. Add your environment-specific deployment steps here.'
            }
        }
    }
}
