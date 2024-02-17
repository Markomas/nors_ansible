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

        stage('Setup haproxy') {
            steps {
                ansiblePlaybook(
                        playbook: 'roles/haproxy/playbook.yml',
                        inventory: 'nors_news_ansible_inventory.yml',
                        credentialsId: 'jenkins_agent',
                        colorized: true)
            }
        }
    }
}