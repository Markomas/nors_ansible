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
            when {
                 expression { params.INFRASTRUCTURE_UPDATE == 'TRUE' }
            }
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
            when {
                 expression { params.INFRASTRUCTURE_UPDATE == 'TRUE' }
            }
            steps {
                ansiblePlaybook(
                        playbook: 'database.yml',
                        inventory: 'nors_news_ansible_inventory.yml',
                        credentialsId: 'jenkins_agent',
                        colorized: true
                        )
            }
        }

        stage('Get release package') {
            steps {
                copyArtifacts filter: 'nors_news.zip', fingerprintArtifacts: true, projectName: env.BUILD_JOB_NAME
            }
        }

        stage('Setup webserver') {
            steps {
                withCredentials([[$class: 'SSHUserPrivateKeyBinding', credentialsId: "app_server", keyFileVariable: 'SSH_PRIVATE_KEY', passphraseVariable: '', usernameVariable: '']]){
                    ansiblePlaybook(
                            playbook: 'webserver.yml',
                            inventory: 'nors_news_ansible_inventory.yml',
                            credentialsId: 'jenkins_agent',
                            colorized: true,
                            extraVars: [
                                deployment_zip_path: 'nors_news.zip'
                                ]
                            )
                }
            }
        }

    }
}
