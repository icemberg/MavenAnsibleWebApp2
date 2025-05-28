pipeline {
    agent any

    tools {
        maven 'Maven'  // Ensure this matches the name configured in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/icemberg/MavenAnsibleWebApp2.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'  // Run Maven build
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/MavenAnsibleWebApp.war',
                                 fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                // Inject the SUDO password into the SUDO_PASS env var
                withCredentials([string(credentialsId: 'SUDO_PASSWORD', variable: 'SUDO_PASS')]) {
                    sh '''
                      # Pass the become password via extra-vars to Ansible
                      ansible-playbook ansible/playbook.yml \
                        -i ansible/hosts.ini \
                        -e ansible_become_pass="$SUDO_PASS"
                    '''
                }
            }
        }
    }
}

