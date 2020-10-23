pipeline {

    agent any

    tools {
        gradle 'gradle'
    }

    stages {
        stage('get resources') {
            steps {
                git url: "https://github.com/dougliu20/sample-spring-boot", branch: "dev"
            }
        }
        stage('build') {
            steps {
                sh 'chmod +x gradlew && ./gradlew build'
            }
        }
        stage('sonarqube') {
            steps {
                withSonarQubeEnv('SonarCloud')
                {
                    sh "./gradlew sonarqube -Dsonar.branch.name=\"dev\""
                    sleep(10)
                }
            }
        }
        stage('sonar check') {
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
                        repoId = 'dougliu/samplesb'
                        image = docker.build(repoId)
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
        stage('deploy') {
            steps {
                sh 'echo kube deploy'
            }
        }
    }
}
