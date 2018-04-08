pipeline {
    agent { label 'espresso' }
    stages{
        /* tests for ansible scripts */
        /* YAML linter */
      stage('Yaml Validation') {
        steps{
           //run yamllint parameter required is the path where the playbooks are stored
          script {
            parallel(
                a: {
                    try {
                        sh "yamllint -c jenkins/yamllint_config */*.yml"
                    } catch (err) {
                        echo "yamllint reported errors, continuing with pipeline"
                        currentBuild.result = 'UNSTABLE'
					          }
                }
            )
          }
        }
      }
      stage('Ansible Lint') {
        steps{
          //run ansible-lint parameter required is the path where the playbooks are stored
          script {
            parallel(
              a: {
                try {
                  echo  sh "ansible-lint -c jenkins/ansible-lint_config */*.yml"
                } catch (err) {
                  echo "ansible-lint reported errors, continuing with pipeline"
                  currentBuild.result = 'UNSTABLE'
                }
              }
            )
          }
        }
      }
      stage('Ansible Playbook Analytics Logged') {
        steps{
          script {
            //running grep to ensure all ansible playbooks are writing logs analytics team needs"
            // currently on searching root if want to search all files use **/*.yml
            def filesToProcess = findFiles(glob: '*.yml')
               //sh "echo grep hosts ${fileToProcess}"
               sh "grep hosts ${fileToProcess}"
             }
           }
         }
      }
  }
  for (def fileToProcess : filesToProcess) {
  post {
    unstable {
        echo 'Todo send a message to slack when pipeline is unstable!'
    }
    failure {
        echo 'Todo send a message to slack when pipeline fails!'
    }
    success {
      shell {
        git archive -o nexus-service.tar.gz --format tgz -v master
      }
      nexusArtifactUploader {
        nexusVersion('nexus3')
        protocol('http')
        nexusUrl('nexus:8081')
        groupId('roles')
        version('3.10')
        repository('ansible')
        credentialsId('nexus-creds')
        artifact {
          artifactId('nexus-artifact-uploader')
          type('raw')
          classifier('debug')
          file('nextcloud-service.tar.gz')
        }
      }
    }
    always {
        echo 'Thank you I have been your Jenkins pipeline today, as a worker in the service industry any and all gratuities are welcome!'
    }
}
}
