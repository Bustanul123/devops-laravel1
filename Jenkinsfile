// node {

//     stage('Checkout') {
//         checkout scm
//     }

//     stage('Build') {
//         docker.image('php:8.2-cli').inside('--entrypoint="" -u root') {
//             sh '''
//             apt-get update
//             apt-get install -y git unzip libzip-dev curl

//             docker-php-ext-install zip

//             curl -sS https://getcomposer.org/installer | php
//             mv composer.phar /usr/local/bin/composer

//             composer install --ignore-platform-req=ext-gd
//             '''
//         }
//     }

//     stage('Testing') {
//         docker.image('ubuntu:24.04').inside('--entrypoint="" -u root') {
//             sh '''
//             echo "Ini adalah test"
//             '''
//         }
//     }

//     stage('Deploy') {
//     docker.image('agung3wi/alpine-rsync:1.1').inside('--entrypoint="" -u root') {
//         sshagent(credentials: ['ssh-prod']) {
//             sh '''
//             mkdir -p ~/.ssh
//             ssh-keyscan -H $PROD_HOST >> ~/.ssh/known_hosts

//             rsync -rav --delete \
//                 --exclude=.env \
//                 --exclude=storage \
//                 --exclude=.git \
//                 ./ bliganz@$PROD_HOST:/home/bliganz/Tugas_Kuliah/CICD/script/prod.kelasdevops.xyz/
//             '''
//         }
//     }
//     }
// }




node {
    // Definisikan variabel di level node agar bisa diakses semua stage
    def PROD_HOST = "172.30.67.52"
    def PROD_USER = "bliganz"
    def PROD_PATH = "/home/bliganz/Tugas_Kuliah/CICD/script/prod.kelasdevops.xyz"

    stage('Checkout') {
        checkout scm
    }

    stage('Build') {
        // Tips: Gunakan image composer resmi agar build lebih cepat (tidak perlu install manual)
        docker.image('composer:2.6').inside('-u root') {
            sh '''
            composer install --ignore-platform-req=ext-gd --no-dev --optimize-autoloader
            '''
        }
    }

    stage('Testing') {
        docker.image('ubuntu:22.04').inside('--entrypoint="" -u root') {
            sh 'echo "Ini adalah test"'
        }
    }

    stage('Deploy') {
        docker.image('agung3wi/alpine-rsync:1.1').inside('--entrypoint="" -u root') {
            sshagent(credentials: ['ssh-prod']) {
                sh """
                # Hapus cache lama dengan mengabaikan Host Key Checking
                #ssh -o StrictHostKeyChecking=no ${PROD_USER}@${PROD_HOST} "rm -f ${PROD_PATH}/bootstrap/cache/packages.php ${PROD_PATH}/bootstrap/cache/services.php"

                # Jalankan rsync dengan mengabaikan Host Key Checking
                rsync -rav --delete -e "ssh -o StrictHostKeyChecking=no" ./ \
                    ${PROD_USER}@${PROD_HOST}:${PROD_PATH}/ \
                    --exclude='public/build' \
                    --exclude='node_modules' \
                    --exclude='vendor' \
                    --exclude='storage' \
                    --exclude='.git' \
                    --exclude='.env'
              
                """
                sh """
           #  ssh -o StrictHostKeyChecking=no ${PROD_USER}@${PROD_HOST} '
             #    cd ${PROD_PATH}
               #  docker-compose down
                # docker-compose up -d
           # '
       #  """
            }
        }
    }
}
