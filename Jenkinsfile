pipeline {
    agent none
    
    stages {

        stage('Setup1') {
            agent { 
                label 'slave ubuntu build'
            }

            steps {
                sh 'echo Setup1'
            }
        }

        stage('Setup2') {
            agent { 
                label 'slave ubuntu build2'
            }

            steps {
                sh 'echo setup2'
            }
        }
    }

    post {
        always {
            deleteDir()
        }
    }
}

_pipeline {
    agent any

    environment {
        UE4_ROOT = '/home/jenkins/UnrealEngine_4.22'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '3', artifactNumToKeepStr: '3'))
    }

    stages {

        stage('Setup') {
            steps {
                sh 'make setup'
            }
        }

        stage('Build') {
            steps {
                sh 'make LibCarla'
                sh 'make PythonAPI'
                sh 'make CarlaUE4Editor'
                sh 'make examples'
            }
            post {
                always {
                    archiveArtifacts 'PythonAPI/carla/dist/*.egg'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'make check ARGS="--all --xml"'
            }
            post {
                always {
                    junit 'Build/test-results/*.xml'
                    archiveArtifacts 'profiler.csv'
                }
            }
        }

        stage('Retrieve Content') {
            steps {
                sh './Update.sh'
            }
        }

        stage('Package') {
            steps {
                sh 'make package'
                sh 'make package ARGS="--packages=AdditionalMaps --clean-intermediate"'
            }
            post {
                always {
                    archiveArtifacts 'Dist/*.tar.gz'
                }
            }
        }

        stage('Deploy for testing') {
            steps {
                sh 'make deploy-for-testing'
            }
        }
    }

}
    post {
        always {
            deleteDir()
        }
    }
