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
                extensions: scm.extensions, userRemoteConfigs: [[credentialsId: '086d9d12-b52a-44b4-9907-e3ea5487b4e1', url: 'https://github.com/madjid04/jenkinsrepo.git']]]
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