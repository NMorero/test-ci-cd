pipeline {
    agent any
    tools {
        maven "maven-nodo-principal"
    }

    stages {
        stage('Build') {
            steps {
                dir ('maven-adderapp') {
                  sh 'mvn -DskipTests clean package'
                }
            }
        }
        stage('Test') {
            steps {
                dir ('maven-adderapp') {
                    sh "mvn test"
                }
            }
        }
        stage('Deploy') {
            steps {

                echo 'Deploying....'
            }
        }
    }

    post {
        always {
            dir('maven-adderapp') {
                junit 'target/surefire-reports/*.xml'
            }
        }
        success {
            dir ('maven-adderapp') {
                script {
                    pom = readMavenPom file: "pom.xml";

                    files = findFiles(glob: "target/*.${pom.packaging}");
                    filePath = files[0].path;

                    nexusArtifactUploader(
                        nexusVersion: "nexus3",
                        protocol: "http",
                        nexusUrl: "${env.NEXUS}:8081",
                        groupId: pom.groupId,
                        version: pom.version,
                        repository: "maven-jenkins",
                        credentialsId: "jenkins-example-user",
                        artifacts: [
                            [artifactId: pom.artifactId,
                            classifier: '',
                            file: filePath,
                            type: pom.packaging],
                            [artifactId: pom.artifactId,
                            classifier: '',
                            file: "pom.xml",
                            type: "pom"]
                        ]
                    );
                }
            }
        }
    }
}