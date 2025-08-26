pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Umair1012/ansible-collections.git'
            }
        }

        stage('Install Collections') {
            steps {
                sh 'ansible-galaxy collection install -r requirements.yml'
            }
        }

        stage('Run Playbook') {
            steps {
                // Use Jenkins credentials: Vault password + uploaded PEM key
                withCredentials([
                    string(credentialsId: 'ansible-vault-pass', variable: 'ANSIBLE_VAULT_PASS'),
                    file(credentialsId: 'aws-pem-key', variable: 'MASTER_KEY_FILE')
                ]) {
                    sh '''
                        # Create temporary file for Vault password
                        echo "$ANSIBLE_VAULT_PASS" > vault_pass.txt

                        # Run Ansible playbook; the inventory/playbook will use lookup('env', 'MASTER_KEY_FILE')
                        ansible-playbook site.yml --vault-password-file vault_pass.txt

                        # Cleanup temporary vault password file
                        rm -f vault_pass.txt
                    '''
                }
            }
        }
    }
}
