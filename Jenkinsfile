pipeline {
    
    agent any

    tools {
        maven "M3"
    }
    triggers {
        cron('H */8 * * *') //regular builds
        pollSCM('* * * * *') //polling for changes, here once a minute
    }

    stages {
        stage('Checking out the repo') {
            steps {
                checkout changelog: true, poll: true, scm: [$class: 'GitSCM', branches: [[name: '*/main']], 
                extensions: scm.extensions, userRemoteConfigs: [[credentialsId: 'github_token', url: 'https://github.com/madjid04/jenkinsrepo.git']]]
                sh "ls -lart ./"    
            }
        }
        stage('Compling ...') {
            steps {
                withMaven(maven:'M3') {
                    sh "mvn compile"
                }
            }
        }        

    }
}