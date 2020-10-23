pipeline {
    agent none
    stages {
        stage('build') {
            agent {
                docker { image 'gradle' }
            }
            steps {
                sh 'chmod +x gradlew && ./gradlew build'
            }
        }
        stage('sonarqube') {
            steps {
                withSonarQubeEnv("SonarCloud")
                {
                    sh "./gradlew sonarqube -Dsonar.branch.name=\"dev\""
                    sleep(10)
                }
            }
        }
        stage("sonar check") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('docker build') {
            steps {
                script {
                   docker.withTool('docker') {
                        repoId = "dougliu/samplesb"
                        image = docker.build(repoId)
                        }
                    }
                }
            }
        }
        stage('docker push') {
            steps {
                script {
                   docker.withTool('docker') {
                        docker.withRegistry("https://registry.hub.docker.com", "dockercred") {
                        image.push()
                        }
                    }
                }
            }
        }
        stage('app deploy') {
            agent {
                docker { image 'busybox' }
            }
            steps {
                sh 'echo kube deploy'
            }
        }
    }