pipeline { 

  agent any

  options {
    ansiColor('xterm')
    timestamps()
  }

  stages {

    stage('Clone repo') {
      steps {
        checkout scm
      }
    }

    stage('Clone scm-ansible') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: 'master']],
          doGenerateSubmoduleConfigurations: false,
          extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: 'scm-ansible']],
          submoduleCfg: [],
          userRemoteConfigs: [[
            url: 'git@bitbucket.org:droptica/scm-ansible.git',
            credentialsId: 'c279944a-fc9e-467b-8361-f5c95d9f3c9d' // dropapi
          ]]
        ])
      }
    }

    stage('Deploy') {
      steps {

        withEnv([
          'target_env=prod',
          'target_version=master',
          'site_name=gra.droptica.pl',
          'repo_url=git@github.com:droptica/DroppyBird.git',
          'skip_build=1',
          'skip_create_files_dir=1',

          'ANSIBLE_HOST_KEY_CHECKING=False',
        ]) {
          sh 'ansible-playbook --key-file /var/jenkins_home/.ssh/id_rsa ./scm-ansible/deploy-new.yml -i "$target_server:59184,"'
        }
      }
    }
  }
}
