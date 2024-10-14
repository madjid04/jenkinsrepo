pipeline {
    
    agent any

    tools {
        maven "M3.6"
    }
    triggers {
        cron('H */8 * * *') //regular builds
        pollSCM('* * * * *') //polling for changes, here once a minute
    }

    stages {
        stage('Checking out the repo') {
            steps {
                checkout changelog: true, poll: true, scm: [$class: 'GitSCM', branches: [[name: '*/main']], 
                extensions: scm.extensions, userRemoteConfigs: [[credentialsId: 'github_token', url: 'https://github.com/jpenekusu/pipeline2.git']]]
                sh "ls -lart ./"    
            }
        }
        stage('Compling ...') {
            steps {
                sh "mvn compile"
            }
        }        
        stage('Testing ...') {
            steps {
                sh "mvn test"
            }
        }        
        stage('Building ...') {
            steps {
                sh "mvn -B -DskipTests clean package"
            }
        }
        stage('Code coverage') {
            steps {
                sh "mvn clean cobertura:cobertura install test -Dcobertura.report.format=xml"
            }
            post {
                always {
                    junit '**/test-reports/*.xml'
                    //step([$class: 'CoberturaPublisher'], autoUpdateHealth: true, coberturaReportFile: '**/target/site/cobertura/*.xml', failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 2, onlyStable: false, sourceEncoding: 'ASCII')
                    step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/coverage.xml', failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 2, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])

                }
            }
        }
        stage('Building and sending results to Sonar ...') {
            steps {
                withSonarQubeEnv(installationName: 'SonarInstall', credentialsId: 'sonar_token') {
                    sh 'mvn -B -DskipTests clean package sonar:sonar'
                }
            }
        }                
    }
    post {
        always { //Send an email to the person that broke the build
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts 'target/*.jar' 
        }
    }
}