node {

    stage('Checkout') {
        checkout scm
    }

    stage('Build') {
        docker.image('php:8.2-cli').inside('--entrypoint="" -u root') {
            sh '''
            apt-get update
            apt-get install -y git unzip libzip-dev curl

            docker-php-ext-install zip

            curl -sS https://getcomposer.org/installer | php
            mv composer.phar /usr/local/bin/composer

            composer install --ignore-platform-req=ext-gd
            '''
        }
    }

    stage('Testing') {
        docker.image('ubuntu:24.04').inside('--entrypoint="" -u root') {
            sh '''
            echo "Ini adalah test"
            '''
        }
    }

    stage('Deploy') {
    docker.image('agung3wi/alpine-rsync:1.1').inside('--entrypoint="" -u root') {
        sshagent(credentials: ['ssh-prod']) {
            sh '''
            mkdir -p ~/.ssh
            ssh-keyscan -H $PROD_HOST >> ~/.ssh/known_hosts

            rsync -rav --delete \
                --exclude=.env \
                --exclude=storage \
                --exclude=.git \
                ./ bliganz@$PROD_HOST:/home/bliganz/Tugas_Kuliah/CICD/script/prod.kelasdevops.xyz/
            '''
        }
    }
    }
}
