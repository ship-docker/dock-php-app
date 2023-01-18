#!/usr/bin/env groovy

node {
    try {
        stage('build') {
            git url: 'git@github.com:ship-docker/dock-php-app.git'

            // Start services (Let docker-compose build containers for testing)
            sh "./develop up -d"

            // Get composer dependencies
            sh "./develop composer install"

            // Create .env file for testing
            sh 'cp .env.example .env'
            sh './develop art key:generate'
            sh 'sed -i "s/REDIS_HOST=.*/REDIS_HOST=redis/" .env'
            sh 'sed -i "s/CACHE_DRIVER=.*/CACHE_DRIVER=redis/" .env'
            sh 'sed -i "s/SESSION_DRIVER=.*/SESSION_DRIVER=redis/" .env'
        }
        stage('test') {
            sh "APP_ENV=testing ./develop test"
        }

        if( env.BRANCH_NAME == 'master' ) {
            stage('package') {
                sh './docker/build'
            }

            stage('deploy') {
                # Enter your own IP-address in here.
                # sh 'ssh -i ~/.ssh/id_sd ubuntu@172.31.52.48 /opt/deploy'
            }
        }

    } catch(error) {
    // Maybe some alerting?
        throw error
    } finally {
        // Spin down containers no matter what happens
        sh './develop down'
    }
}
