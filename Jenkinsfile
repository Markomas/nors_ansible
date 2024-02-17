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
