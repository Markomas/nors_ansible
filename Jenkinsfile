pipeline {
    agent any

    options {
       ansiColor('xterm')
    }

    stages {
        stage('Get ansible inventory') {
            steps {
                copyArtifacts filter: 'nors_news_ansible_inventory.yml', fingerprintArtifacts: true, projectName: env.TERRAFORM_JOB_NAME
            }
        }

        stage('Setup loadbalancer') {
            steps {
                ansiblePlaybook(
                        playbook: 'loadbalancer.yml',
                        inventory: 'nors_news_ansible_inventory.yml',
                        credentialsId: 'jenkins_agent',
                        colorized: true
                        )
            }
        }

        stage('Setup database') {
            steps {
                ansiblePlaybook(
                        playbook: 'database.yml',
                        inventory: 'nors_news_ansible_inventory.yml',
                        credentialsId: 'jenkins_agent',
                        colorized: true
                        )
            }
        }

        stage('Setup webservers') {
            steps {
                withCredentials([[$class: 'SSHUserPrivateKeyBinding', credentialsId: "app_server", keyFileVariable: 'SSH_PRIVATE_KEY', passphraseVariable: '', usernameVariable: '']]){
                    echo "this is a string ${SSH_PRIVATE_KEY}";
                }
                ansiblePlaybook(
                        playbook: 'webserver.yml',
                        inventory: 'nors_news_ansible_inventory.yml',
                        credentialsId: 'jenkins_agent',
                        colorized: true
                        )
            }
        }

    }
}
