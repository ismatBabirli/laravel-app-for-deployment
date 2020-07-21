/* groovylint-disable CompileStatic, SpaceAroundMapEntryColon */
pipeline {
    agent {
        docker {
            image 'ismatbabir/laravel-jenkins:latest'
            args '-u root'
        }
    }
    stages {
        stage('build') {
            steps {
                sh 'composer install --prefer-dist --no-ansi --no-interaction --no-progress --no-scripts'
                sh 'cp .env.example .env'
            }
        }

//         stage('Test phpunit') {
//             steps {
//                 sh './vendor/bin/phpunit'
//             }
//         }

        stage('Iint ssh') {
            steps {
                withCredentials(bindings: [
                      file(credentialsId: 'jenkins_deploy_private_key', variable: 'PRIVATE_KEY')]) {
                    sh 'eval "$(ssh-agent -s)"'
                    sh 'mkdir -p ~/.ssh'
                    sh 'cp \$PRIVATE_KEY ~/.ssh/id_rsa'
                    sh 'chmod 600 ~/.ssh/id_rsa'
                    sh 'chmod 700 ~/.ssh'
                    }
            }
        }


        stage('Deploy To Production') {
            when  {
                branch 'master'
            }
            steps {
                sh 'ssh-keyscan 18.218.90.65 >> ~/.ssh/known_hosts'
                sh 'php artisan deploy 18.218.90.65 -s upload'
            }
        }

        stage('Deploy To Development') {
            when  {
                branch 'development'
            }
            steps {
                sh 'ssh-keyscan example.com >> ~/.ssh/known_hosts'
                sh 'php artisan deploy example.com -s upload'
            }
        }
    }
}
