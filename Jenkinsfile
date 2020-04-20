@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                withAWS(region:'us-east-2',credentials:'aws-jenkins') {
                     s3Upload(bucket: 'yazeeh-jenkins', workingDir:'jenkins/scripts', includePathPattern:'**/*');
                }
            }
        }
    }
}